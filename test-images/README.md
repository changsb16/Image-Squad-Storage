# Image Collage — Gold Standard Test Set

20 posts. Post them one folder at a time in the private Engage community.
Each image is labeled with its dimensions and what to watch for.

**Color guide:** pink = face/people · orange = text/infographic · green = general · teal = landscape · indigo = portrait · purple = square · emerald = panorama · slate = dimension edge case · red = duplicate

---

## Tier 1 — Content Categories

### Post 01 · Face: Single Centered (1 image)
`post-01_face-single-centered/`
- Face circle centered in frame
- **Expected:** Center crop keeps face visible. Easy baseline.

### Post 02 · Face: Off-Center (2 images)
`post-02_face-off-center/`
- Image 1: face near left edge
- Image 2: face in bottom third
- **Expected:** If face detection is off, center crop will clip the face. **Failure = face at edge or cut off.**

### Post 03 · Face: Groups (3 images)
`post-03_face-groups/`
- 2-person, small group (3–5), large group (10+)
- **Expected:** Large group — edge people may get clipped by center crop. Watch the outer columns.

### Post 04 · Text: Full Infographic (1 image)
`post-04_text-full-infographic/`
- Tall 800×1600 infographic with text lines
- **Expected: RESIZE, not crop.** Any cropping destroys the text. Whole image should be readable.

### Post 05 · Text: Mixed Types (3 images)
`post-05_text-mixed-types/`
- Announcement banner (1200×628), slide screenshot (1280×720), chart/graph (1000×750)
- **Expected:** All three should resize or be handled non-destructively. Cropping cuts off axis labels and headlines.

### Post 06 · Text: Logos (2 images)
`post-06_text-logos/`
- Horizontal logo (1200×400, 3:1 wide), square logo (600×600)
- **Expected:** Wide logo gets heavy crop if treated as a photo. Square logo should fit cleanly.

### Post 07 · General: Landscape Photos (3 images)
`post-07_general-landscape-photos/`
- Nature, architecture, product — all landscape
- **Expected:** No focal point sensitivity. Center crop is fine. Baseline easy case.

---

## Tier 2 — Dimension Edge Cases

### Post 08 · 4 Squares → 2×2 Grid (4 images)
`post-08_dim-square-grid/`
- 4 identical 800×800 square images
- **Expected:** Should render as a balanced 2×2 grid without padding artifacts.

### Post 09 · Ultra-Wide Panorama (1 image)
`post-09_dim-panorama/`
- 1800×600 (3:1 ratio)
- **Expected:** Very heavy crop or letterboxing. Does layout break or handle gracefully?

### Post 10 · Ultra-Tall Portrait (1 image)
`post-10_dim-very-tall/`
- 600×1800 (1:3 ratio)
- **Expected:** Extreme tall — does the card expand? Overflow? Crop aggressively?

### Post 11 · Near-Square Variants (3 images)
`post-11_dim-near-square/`
- 4:3 landscape (800×600), 3:4 portrait (600×800), 1:1 square (700×700)
- **Expected:** All three are close to target slot ratios. Should all render cleanly with minimal crop.

### Post 12 · Size Extremes (2 images)
`post-12_dim-size-extremes/`
- Tiny: 300×200 (under 400px shortest side)
- Large: 3840×2160 (4K)
- **Expected:** Tiny → upscales without pixelation. 4K → downscales without stripping crop metadata.

---

## Tier 3 — Collage Composition Scenarios

### Post 13 · All Portrait (3 images)
`post-13_scenario-all-portrait/`
- 3× 540×960 (9:16)
- **Expected:** Layout rebalances to accommodate all-tall inputs. Watch for stacking or awkward padding.

### Post 14 · All Landscape (3 images)
`post-14_scenario-all-landscape/`
- 3× 960×540 (16:9)
- **Expected:** Baseline easiest case. Should look clean. Use as visual reference point.

### Post 15 · Mixed 3-Pack (3 images)
`post-15_scenario-mixed-3pack/`
- 1 portrait (hero) + 2 landscape (secondary)
- **Expected:** Most common real-world post type. Hero should be prominent; secondaries should balance.

### Post 16 · Text + Photos (3 images)
`post-16_scenario-text-and-photos/`
- 1 infographic (tall, orange) + 2 face photos (pink)
- **Expected:** Infographic → resize. Face photos → crop. Mixed treatment in one collage.

### Post 17 · Duplicate Image Bug (2 images)
`post-17_scenario-duplicate-bug/`
- Same image posted twice (both copies are identical red images)
- **Expected:** System should either prevent this or handle it gracefully. **This is a known bug.**

### Post 18 · Four-Image Grid (4 images)
`post-18_scenario-four-grid/`
- 2 landscape + 2 square
- **Expected:** 2×2 grid. All four slots should be visually balanced.

### Post 19 · Nine-Image Max (9 images)
`post-19_scenario-nine-max/`
- 9 images: mix of portrait, landscape, square, face
- **Expected:** Max collage count. Layout must not collapse, overflow must show correct +N count.

### Post 20 · Extreme Mix (4 images)
`post-20_scenario-extreme-mix/`
- 1 panorama (3:1) + 1 off-center face (4:5) + 1 infographic (2:3) + 1 square (1:1)
- **Expected:** Worst-case heterogeneous input. Watch for layout distortion, missed face detection, and text cropping simultaneously.

---

## What to Record When Testing

For each post, note:
- Platform (iOS / Android / Web)
- Does the collage look right at a glance? (gut check)
- Any images visibly clipped in a bad way?
- For face images: is the face circle fully visible?
- For text images: is the text readable / not cropped?
- For +N overflow (posts 19, 20): is the count accurate?
