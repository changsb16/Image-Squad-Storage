# Design Documentation v2: Content-Aware Image Collage

## Overview

This document extends the v1 collage design spec to add **face detection**, **adaptive focal-point cropping**, and **text image classification**. The core grid structure and slot boundaries are unchanged; what changes is how each image's aspect ratio is selected and how it is cropped.

**Two problems with v1 that this spec addresses:**

1. **Hero stretching** — When face detection identifies a face and that crop is forced into a fixed aspect ratio that doesn't match the face's natural framing, the result is stretched or distorted.
2. **Text image destruction** — Infographics, slide screenshots, and banners lose legibility when cropped to a fixed aspect ratio. The v1 spec noted "resize not crop" as the right approach; this spec formalizes what that means in practice.

**What's preserved from v1:**
- Web: 1 hero + max 3 secondaries
- Mobile: 1 hero + max 2 secondaries
- 5+ images: "+N more" overlay on the last visible tile
- Hero is always the first image in the post array (no reordering in this version)
- Secondary images: 4:3 aspect ratio for photo content

---

## Design System

### Allowed Aspect Ratios

The v2 system expands from 2 fixed hero ratios to 4 dynamically selected ones, while removing the extremes that v1 already cautioned against (9:16 is too tall for feed scrollability; 16:9 leaves tiny secondary tiles).

| Ratio | Type | Hero Use Case | Secondary Use Case |
|-------|------|---------------|-------------------|
| **4:5** | Portrait (default) | Face portraits, most user content, Instagram standard | — |
| **2:3** | Tall portrait | Very tall source images without faces | — |
| **1:1** | Square | Near-square sources | Two-image layouts with landscape/square hero |
| **3:2** | Landscape | Landscape photos, face in wide image | — |
| **4:3** | Secondary standard | — | All photo secondary slots (unchanged from v1) |

**Why 4 hero ratios instead of v1's 2:**
The original 4:5 / 3:2 binary worked well for center cropping where the crop region was always the image center. With face detection, the crop region moves to the focal point — and a fixed target ratio that diverges significantly from the source image's natural proportions forces a very tight or very wide crop window, which is where stretching occurs. Expanding to 4 ratios means the selected ratio is always closer to the source's natural shape, so the adaptive crop region (see Section 3) can frame the subject without distorting.

---

## Content Classification Pipeline

Before any layout or cropping decisions are made, each image is assigned a content type. This runs client-side before render.

### Step 1: Text Detection (fast path, <100ms)

Run pixel contrast analysis on a downsampled canvas representation of the image:

```
text_score = (edge_density × 0.4) + (color_palette_sparsity × 0.3) + (contrast_frequency × 0.3)
```

- **edge_density**: ratio of high-contrast pixel transitions (text has sharp edges)
- **color_palette_sparsity**: text images use few distinct colors vs. photos
- **contrast_frequency**: text regions show high-frequency contrast changes in horizontal/vertical sweeps

**Threshold:** `score > 0.65` → classify as `TEXT_IMAGE`

No network call, no model download, runs entirely on canvas pixel data. Backend classification via indi8 is honored when available and takes precedence; pixel analysis is the client-side fallback.

### Step 2: Face Detection (async, 200–500ms)

Client-side via **face-api.js** (TensorFlow.js-based).

> **Note:** face-api.js requires OSS approval at Microsoft. Web baseline uses dimension-only classification until that approval clears. Android already uses ML Kit face-aware cropping (Subodh's PR #265283). This step is a no-op on web until the approval is in place.

Output per image:
```typescript
{
  hasFace: boolean,
  focalPoint: { x: number, y: number },      // normalized 0.0–1.0
  boundingBox: { top, left, width, height }   // normalized 0.0–1.0
}
```

For multiple faces: compute the weighted center of mass across all bounding boxes.

### Step 3: Assign Content Type

```
if text_score > 0.65                    → TEXT_IMAGE
else if hasFace && height > width       → FACE_PORTRAIT
else if hasFace && width >= height      → FACE_LANDSCAPE
else if width/height < 0.65            → PHOTO_PORTRAIT (very tall)
else if width/height > 1.18            → PHOTO_LANDSCAPE
else if width/height between 0.85–1.18 → PHOTO_SQUARE
else                                    → PHOTO_PORTRAIT (fallback)
```

---

## Dynamic Aspect Ratio Selection

### Hero Ratio

Each image's content type maps to a hero aspect ratio:

```
FACE_PORTRAIT   → 4:5   standard portrait; adaptive crop handles focal point
FACE_LANDSCAPE  → 3:2   matches natural orientation, frames face well
PHOTO_PORTRAIT  → 2:3   if source width/height ratio < 0.65 (very tall)
                  4:5   otherwise
PHOTO_LANDSCAPE → 3:2
PHOTO_SQUARE    → 1:1   source ratio 0.85–1.18
TEXT_IMAGE      → no ratio locked; variable height (see Text Image Handling)
fallback        → 4:5
```

### Layout Routing by Hero Ratio

| Hero Ratio | Layout Mode | Structure |
|------------|------------|-----------|
| 4:5 | **Portrait-left** | Hero: left column, 66% width. Secondaries: stacked right. |
| 2:3 | **Portrait-left** | Same as 4:5, hero column is taller. |
| 1:1 | **Landscape-top** | Hero: top row, full width. Secondaries: row below. |
| 3:2 | **Landscape-top** | Same as v1 landscape layout. |

**Why 1:1 routes to landscape-top:**
A 1:1 hero in portrait-left at 66% card width leaves a 34%-wide secondary column. Three stacked 4:3 secondary tiles in that column would each be roughly 34% wide × 45% tall, and the stack total (~135% of card width) would far overflow the 100%-tall hero. Landscape-top avoids this: the secondary row sits neatly beneath the square hero.

---

## Adaptive Crop Region (No Stretching)

When a face is detected, the crop is calculated around the focal point while strictly maintaining the target aspect ratio — never stretching.

**Algorithm:**

```
1. face_cx = focalPoint.x × image.width
   face_cy = focalPoint.y × image.height

2. For target ratio (W:H):
   candidate_height = min(image.height, image.width / (W / H))
   candidate_width  = candidate_height × (W / H)

3. Center on face:
   crop_left = face_cx - candidate_width / 2
   crop_top  = face_cy - candidate_height / 2

4. Clamp to image bounds:
   crop_left = max(0, min(crop_left, image.width  - candidate_width))
   crop_top  = max(0, min(crop_top,  image.height - candidate_height))

5. Apply via object-position (CSS) or canvas extraction
```

**Key constraint:** Never stretch. If the face bounding box doesn't fit cleanly, show slightly more background context rather than squeeze the face.

**Fallback for tight crops:**
If the candidate crop region covers less than 60% of the face bounding box, step down to a slightly looser ratio and re-run:
- `FACE_PORTRAIT` at 4:5 → try 2:3 (taller, more room for a tall face)
- `FACE_LANDSCAPE` at 3:2 → try 4:5 (safer fallback)

**No face detected:** fall back to center cropping (`object-position: center`), same as v1.

---

## Text Image Handling

Text images are never cropped. They are rendered with `object-fit: contain` in a variable-height slot.

### Rendering Rules

| Property | Value |
|----------|-------|
| `object-fit` | `contain` (not `cover`) |
| Slot height | Expands to natural aspect ratio |
| Max height cap | `min(natural_height, 2 × reference_hero_height)` — prevents runaway infographics |
| Background fill | `#F3F3F3` (neutral, for letterboxed areas) |

The reference hero height is calculated from the 3:2 ratio at the current card width (e.g., a 600px card → 3:2 hero = 400px → max text slot height = 800px).

### Layout Integration

**Text image in hero slot:**
Always uses landscape-top routing regardless of source orientation (full-width, contain, variable height). Secondary images go in a row beneath it.

**Text image in secondary slot:**
That slot expands independently to fit the image's natural ratio. Other secondary slots remain fixed at 4:3. CSS:
```css
.secondary-grid {
  grid-auto-rows: minmax(120px, max-content); /* for rows with text images */
}
.secondary-slot-photo {
  height: <fixed 4:3 height>;
}
```

**+N overflow overlay:**
Applied normally regardless of whether the last visible slot is a text image or photo.

---

## Layout Strategy

Grid boundaries from v1 are unchanged. Diagrams below cover all hero ratio and image count combinations for web and mobile.

---

### 1 Image

**Portrait (4:5)**
```
┌──────────────────┐
│                  │
│        1         │
│      (4:5)       │
│                  │
└──────────────────┘
```

**Tall Portrait (2:3)**
```
┌──────────────────┐
│                  │
│        1         │
│      (2:3)       │
│                  │
│                  │
└──────────────────┘
```

**Square (1:1)**
```
┌──────────────────┐
│        1         │
│      (1:1)       │
└──────────────────┘
```

**Landscape (3:2)**
```
┌──────────────────┐
│        1         │
│      (3:2)       │
└──────────────────┘
```

---

### 2 Images

**Portrait hero (4:5 or 2:3)** — both tiles at hero ratio, 1fr 1fr grid
```
┌─────────┬────────┐
│         │        │
│    1    │   2    │
│ (ratio) │(ratio) │
│         │        │
└─────────┴────────┘
```

**Landscape/Square hero (3:2 or 1:1)** — both tiles at 1:1, 1fr 1fr grid
```
┌─────────┬────────┐
│    1    │   2    │
│  (1:1)  │ (1:1)  │
└─────────┴────────┘
```

---

### 3 Images

**Portrait hero (4:5) — Web & Mobile**
```
┌───────────┬──────┐
│           │  2   │
│     1     │      │
│   (4:5)   ├──────┤
│           │  3   │
└───────────┴──────┘
```
Hero: left column, 66% width, 4:5 locked. Secondaries: right column, flexible height (~50% each), no locked ratio — `justifyContent: space-between`. Same Facebook/LinkedIn approach as v1.

**Tall Portrait hero (2:3) — Web & Mobile**
```
┌───────────┬──────┐
│           │  2   │
│     1     │      │
│   (2:3)   ├──────┤
│           │  3   │
│           │      │
└───────────┴──────┘
```
Same structure as 4:5. Hero column is taller; secondaries each take ~50% of the taller height.

**Landscape/Square hero (3:2 or 1:1) — Web & Mobile**
```
┌────────────────────┐
│         1          │
│   (3:2 or 1:1)     │
├──────────┬─────────┤
│    2     │    3    │
│  (4:3)   │  (4:3)  │
└──────────┴─────────┘
```
Hero: top row, full width. Secondaries: bottom row, side-by-side, 4:3 each.

---

### 4+ Images — Web (max 3 secondaries)

**Portrait hero (4:5)**
```
┌───────────┬──────┐
│           │  2   │
│     1     ├──────┤
│   (4:5)   │  3   │
│           ├──────┤
│           │  4   │
└───────────┴──────┘
```
Hero: left column, 66%, 4:5. Secondaries: right column, 3 stacked at 4:3, `justifyContent: space-between`.

**Tall Portrait hero (2:3)**
```
┌───────────┬──────┐
│           │  2   │
│     1     ├──────┤
│   (2:3)   │  3   │
│           ├──────┤
│           │  4   │
└───────────┴──────┘
```
Same structure as 4:5. Taller hero column means each secondary tile is slightly shorter.

**Landscape/Square hero (3:2 or 1:1)**
```
┌──────────────────────┐
│          1           │
│    (3:2 or 1:1)      │
├───────┬───────┬──────┤
│   2   │   3   │   4  │
│ (4:3) │ (4:3) │(4:3) │
└───────┴───────┴──────┘
```
Hero: top row, full width. Secondaries: bottom row, 3 tiles at 4:3 each.

### 5+ Images — Web
Same as 4+ with "+N more" overlay on the 4th visible tile.

---

### Mobile Layouts (max 2 secondaries)

**Portrait hero (4:5)**
```
┌───────────┬──────┐
│           │  2   │
│     1     ├──────┤
│   (4:5)   │  3   │
└───────────┴──────┘
```

**Tall Portrait hero (2:3)**
```
┌───────────┬──────┐
│           │  2   │
│     1     │      │
│   (2:3)   ├──────┤
│           │  3   │
└───────────┴──────┘
```

**Landscape/Square hero (3:2 or 1:1)**
```
┌────────────────────┐
│         1          │
│   (3:2 or 1:1)     │
├──────────┬─────────┤
│    2     │    3    │
│  (4:3)   │  (4:3)  │
└──────────┴─────────┘
```

**4+ images on mobile** — "+N more" overlay on last visible secondary. Same diagrams as above; only the overlay count changes.

---

### Text Image in Collage

**Text image as hero** — landscape-top, contain, variable height
```
┌────────────────────┐
│         1          │
│   TEXT IMAGE       │  ← object-fit: contain; height expands to natural ratio
│   (variable ht.)   │     capped at 2× reference hero height
├──────────┬─────────┤
│    2     │    3    │
│  (4:3)   │  (4:3)  │
└──────────┴─────────┘
```

**Text image as secondary** — that slot expands, photo slots remain fixed
```
┌───────────┬────────┐
│           │   2    │
│     1     │ (4:3)  │
│   (4:5)   ├────────┤
│           │   3    │
│           │  TEXT  │  ← object-fit: contain; slot expands to natural ratio
│           │ (var.) │
└───────────┴────────┘
```

---

## Implementation Notes

### Image Cropping
- **Photos:** `object-fit: cover` + `object-position` set to computed focal-point percentages from the adaptive crop algorithm
- **Text images:** `object-fit: contain` + neutral background fill
- **Fallback (no face detected):** `object-fit: cover` + `object-position: center` (same as v1)

### Click-to-Preview
Unchanged from v1 — full-screen modal, dark overlay, close button, click outside to close.

### Responsive Width
Unchanged from v1 — default 600px, adjustable 320–900px.

---

## Design Decisions & Trade-offs

### Why adaptive crop instead of letterboxing?
**Letterboxing** (padding to preserve the face's natural aspect ratio) avoids all distortion but introduces visual noise — empty bars around the subject. **Adaptive cropping** keeps the slot clean at the cost of potentially showing slightly less background context. For a social feed where images are small, clean slots beat letterboxed ones. Letterboxing remains an option for a follow-on experiment if customer feedback shows visible distortion.

### Why FACE_PORTRAIT maps to 4:5 (not 2:3)?
4:5 is the Instagram standard for portrait content and the most common phone photo crop. For faces specifically, the adaptive crop algorithm is trusted to shift the crop window to frame the face well — the ratio selection doesn't need to be as tight. 2:3 is reserved for non-face content where we're trying to minimize letterboxing for very tall source images.

### Why TEXT_IMAGE always uses landscape-top?
Text images expand to variable height. Landscape-top (full-width hero) is the safest container for that: the secondary row sits below and is unaffected. Portrait-left with a variable-height hero would cause the secondary stack to have unpredictable alignment.

### Secondary ratio stays at 4:3
Secondary slots are small enough that the dynamic ratio selection would produce negligible visual benefit while significantly increasing layout complexity. 4:3 remains a safe, versatile ratio for all secondary photos.

---

## Constraints & Decisions

| Decision | Rationale |
|----------|-----------|
| Face detection is client-side only; no backend identity recognition | Decided March 4 (Decisions Log) |
| face-api.js requires OSS approval at Microsoft | Web face detection is blocked until approval clears; Android uses ML Kit (Subodh's PR #265283 already up) |
| Web baseline: dimension-only classification, no face detection | Dimension heuristics still improve on fixed ratios and ship without OSS blockers |
| Text classification: pixel analysis first, indi8 deferred | Collect real customer examples before committing to backend classification pipeline |
| Text images: resize not crop | Decided March 10 (Decisions Log); this spec formalizes the rendering and layout approach |
| Hero ordering: first image in array is always hero | Hero selection logic is deferred to a future version |
| Center crop as fallback | For images where face detection fails or returns low confidence |

---

## Test Scenarios (Gold Standard Test Set)

| Post | Content | Expected v2 Behavior |
|------|---------|----------------------|
| Post 01 | Single centered face | `FACE_PORTRAIT` → 4:5 hero; adaptive crop centers face |
| Post 02 | Off-center faces (left edge, bottom third) | Focal point clamped to face position; face not clipped |
| Post 03 | Face groups (2-person, 3–5, 10+) | Weighted center of mass → crop encompasses most faces |
| Post 04 | Tall infographic 800×1600 | `TEXT_IMAGE` → landscape-top, contain rendering, variable height |
| Post 05 | Mixed text types (banner, slide, chart) | Text slots expand; photo secondaries fixed 4:3 |
| Post 08 | 4 square images | `PHOTO_SQUARE` → 1:1 hero → landscape-top layout |
| Post 09 | Ultra-wide panorama 1800×600 (3:1) | `PHOTO_LANDSCAPE` → 3:2 hero; heavy crop accepted |
| Post 10 | Ultra-tall portrait 600×1800 (1:3) | Source ratio 0.33 < 0.65 → `PHOTO_PORTRAIT` → 2:3 hero |
| Post 15 | Mixed 3-pack (1 portrait hero + 2 landscape) | Dynamic ratio per image; portrait-left layout for portrait hero |
| Post 16 | Text + photos mixed | Text → variable-height slot; photos at 4:3 |
| Post 20 | Extreme mix (panorama + off-center face + infographic + square) | Each image classified independently; content type drives ratio + crop per slot |

---

## Future Considerations

### Smart Cropping for Secondary Images
Currently secondary images use center cropping at 4:3. A follow-on could apply the same face-detection focal point crop to secondaries, particularly important for Posts 02 and 03 (off-center and group faces appearing in secondary slots).

### User-Controlled Aspect Ratio Override
Default to system-chosen ratio, allow the publisher to override if the system's choice isn't right. Scope: 7+ week initiative pending telemetry on how often users encounter poor automatic selections.

### Separate Post Type for Text/Infographic Content
LinkedIn's document carousel approach — dedicated post type for text-heavy images, not part of the photo collage. Worth evaluating if infographic posts become a significant Engage use case. Prerequisite: collect customer examples first.

### indi8 Classification Integration
Backend text classification via indi8 would be more accurate than client-side pixel analysis and would enable better handling of edge cases (charts with images, mixed slides). Deferred pending real customer usage data.
