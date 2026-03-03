# Competitor Intelligence — Media Experiences
**AI FWD Squad — March 2026**

Analysis focused on three priority work streams. Image collage layout deprioritized (Miriam's deploy already in progress).

---

## 1. Portrait Video in Feed

### The Question
When a portrait (9:16) video appears in the feed, does the card expand to full portrait height, crop to a fixed ratio, or letterbox?

---

### TikTok
The feed *is* portrait video. 9:16 is the native and only format — the card fills the full screen height. Other aspect ratios are letterboxed with black bars. The entire product is built around this assumption: no feed card concept, no mixed content, full immersion.

- **Feed card height:** Full screen (no cap)
- **Non-portrait content:** Letterboxed
- **Mobile vs. desktop:** TikTok is mobile-only by design; desktop is secondary

---

### Instagram
Split behavior by surface:

- **Reels tab:** Full 9:16 portrait, full screen — same as TikTok
- **Home feed:** Portrait video is **capped at 4:5** (1080×1350px). If you post a 9:16 video to the regular feed, the top and bottom are cropped to 4:5. Instagram routes all video to Reels specifically to solve this — the home feed is not where portrait video lives for them.
- **Grid thumbnail:** Shown at 3:4

Instagram's answer to the tension between portrait video and a mixed feed was to create a separate full-screen surface (Reels), not to change the feed card height.

---

### LinkedIn
Vertical video is supported but behavior depends on platform:

- **Mobile (iOS 9.14+ / Android 0.406.59+):** Renders at native 9:16 portrait height; card expands
- **Desktop:** Padding and black bars around portrait video — no full portrait height
- **Recommended ratio:** LinkedIn recommends 4:5 as the practical compromise — better desktop rendering while still gaining vertical mobile real estate
- **Context:** 80%+ of LinkedIn video is watched on mobile, which has driven their push toward vertical

---

### Gap vs. Engage
Engage currently forces all portrait video into a landscape rectangle — the card doesn't expand because `videoWidth` and `videoHeight` return `null` for SharePointVideoLink media type, so the portrait code path never fires. The backend data is correct; it's just getting dropped during Filelink serialization (Joshua's fix).

---

### Recommendation for Engage
**Immediate (Track 1 — Joshua's fix):** Once dimensions are returned correctly, the portrait code path already exists and works (confirmed by Richard's local test). The card will render at native aspect ratio. This is the "works as designed" milestone.

**Next (Track 2 — Shawn + David spike):** The TikTok-style full-feed-height card is a separate, larger design decision. Instagram's model (4:5 in the feed, full 9:16 on a dedicated video surface) is probably the right Engage analog — less disruptive to the mixed feed, but a better mobile experience than landscape forcing.

**Key decision to make:** Does portrait video get 4:5 in-feed treatment, or does Engage need a Reels-like dedicated surface eventually?

---

## 2. Full-Screen Gallery Viewer with Comments

### The Question
Can users view images full-screen while keeping thread comments and reactions visible — without losing their place in the post?

---

### Platform Layouts

**Facebook (desktop) — gold standard**

```
┌──────────────────────────────────────────────────────────────────┐
│  ← (prev)  │                               │  × close           │
│            │   [IMAGE — fills ~65% width]  │  ┌──────────────┐  │
│            │                               │  │ @Author      │  │
│            │                               │  │ Caption text │  │
│            │                               │  ├──────────────┤  │
│            │                               │  │ 👍 ❤️ 😂      │  │
│            │                               │  ├──────────────┤  │
│  → (next)  │                               │  │ Comment      │  │
│            │                               │  │ Comment      │  │
│            │                               │  │ Comment      │  │
│            │                               │  ├──────────────┤  │
│            │                               │  │ [Write a     │  │
│            │                               │  │  comment...] │  │
└────────────┴───────────────────────────────┴──────────────────┘
```

- Comments panel is **always visible alongside the image** — no scrolling away from the photo
- React, like, and reply without leaving the viewer
- Navigate between photos in an album with arrow keys or clicks
- Right panel scrolls independently; image stays fixed
- Designed to solve: "users had to scroll down to read comments, losing sight of the image entirely"

**Instagram (mobile)**

```
┌───────────────────────┐
│  ← back    @username  │
├───────────────────────┤
│                       │
│       [IMAGE]         │
│   (square or 4:5)     │
│    ·  ●  ·   (dots)   │  ← carousel position indicator
├───────────────────────┤
│  ♡  💬  ✈  [bookmark] │
│  123 likes            │
│  @user Caption...     │
│  View all 47 comments │
├───────────────────────┤
│  [Add a comment...]   │
└───────────────────────┘
```

- Comments are **below the image**, not alongside it
- Scrolling down to read comments causes the image to scroll out of view
- Swipe left/right on image for carousel navigation — smooth, no context loss for the image
- No side-by-side on mobile (screen too narrow); comments are below

**LinkedIn (desktop)**

```
┌──────────────────────────────────────────────┐
│                                  × close      │
│   ┌──────────────────────────────────────┐   │
│   │                                      │   │
│   │         [IMAGE — centered]           │   │
│   │                                      │   │
│   └──────────────────────────────────────┘   │
│                                               │
│   @Author · Caption text                     │
│   👍 ❤️  123 reactions · 45 comments         │
│                                               │
│   Comment · Comment (truncated preview)      │
│   [See all comments →] ← takes you out       │
└──────────────────────────────────────────────┘
```

- Opens a modal/overlay but comments are truncated
- "See all comments" navigates away from the viewer — context lost
- Least integrated of the three; no persistent comment panel

---

### Competitive Ranking

| Platform | Comments during viewing | React without leaving | Navigate between images |
|----------|------------------------|----------------------|------------------------|
| Facebook | Always visible (right panel) | Yes | Yes (arrows) |
| Instagram | Below image (scroll away) | Yes | Yes (swipe) |
| LinkedIn | Truncated, click-away | No | No |
| **Engage today** | **Not accessible** | **No** | **No** |

---

### Gap vs. Engage
Engage has no full-screen viewer at all. Tapping an image opens a basic lightbox with no thread context — reactions and comments are completely inaccessible without leaving the image view.

---

### Recommendation for Engage
**Desktop:** Build toward Facebook's pattern — image fills left ~65%, comments panel on the right is persistent and scrollable. Reactions and reply input stay in the panel. Users never lose the image.

**Mobile:** Instagram's pattern is sufficient — image on top, comment section accessible below (or via a slide-up bottom sheet). Side-by-side isn't practical on mobile widths. Swipe navigation between multiple images in a post is high-value and should be included.

**Critical feature:** Non-destructive navigation — swipe through images in a multi-image post without being ejected to the feed. This is what makes both Facebook and Instagram's viewers feel native.

---

## 3. Text / Infographic Image Treatment

### The Question
Do LinkedIn or Facebook detect text-heavy images and treat them differently from photos? Do they crop, resize, or letterbox?

---

### LinkedIn
LinkedIn does not have publicly documented text-aware classification that changes the rendering path for organic posts. The same display rules apply to all images:

- **Feed rendering:** Images are display-cropped at the rendering layer (file is preserved) to fit within the feed column
- **Aspect ratio cap:** Effective max in-feed height is approximately 4:5 before LinkedIn clips/collapses the image with an expand option
- **Infographics (very tall):** A 3000px-tall infographic renders clipped in the feed with a "See more" or expand prompt — the full image is accessible but not shown inline
- **Safe zone:** LinkedIn guidance is to keep text center-aligned, away from edges, since previews (notifications, link shares) may crop further
- **Their real solution:** LinkedIn introduced **document/PDF carousel posts** specifically for the infographic use case. Creators upload a multi-page PDF that renders as a swipeable slide deck — this is now the recommended format for tall infographic content on LinkedIn

**Key insight:** LinkedIn didn't solve the infographic problem with smarter rendering. They solved it by creating a different post type.

---

### Facebook
Facebook enforces a supported aspect ratio range of approximately 9:16 (portrait) to 16:9 (landscape):

- **Images outside this range:** Letterboxed or display-cropped. Very tall infographics (taller than 9:16) are cropped to 9:16 at the display layer, with tap-to-expand
- **Text classification:** Facebook/Meta does perform image text detection at scale — but it's used for **ad reach scoring** (images with >20% text coverage receive reduced distribution), not for changing the rendering path of organic posts. No "resize instead of crop" treatment for text images.
- **Letterboxing:** Narrow portrait images in some contexts get a blurred background fill (letterboxing) rather than stretching
- **File is not modified:** All cropping is a display-layer behavior only

---

### Comparative Summary

| Dimension | LinkedIn | Facebook |
|-----------|----------|----------|
| Aspect ratio cap | ~4:5 before clipping | 9:16 max before clipping |
| Tall infographic handling | Clipped in feed, expand to see full | Cropped to 9:16, tap to expand |
| Text-aware rendering | Not documented for organic posts | Not for organic (ad reach only) |
| Resize instead of crop | No | No |
| Destructive crop | No — display only | No — display only |
| Letterboxing | Not documented | Yes, blurred background fill |
| Recommended infographic format | PDF/document carousel | Carousel/album |

---

### Gap vs. Engage
Engage's planned treatment — classify text-heavy images at index time (indi8) and resize instead of crop — is **differentiated**. Neither LinkedIn nor Facebook does this for organic posts. The decision is sound: cropping text images is clearly worse. Resize is imperfect but at least preserves readability.

The deeper question raised by LinkedIn's approach: is indi8 classification the right investment within 7 weeks, or is a **document/carousel post type** the more complete solution for users who regularly post infographic content?

---

### Recommendation for Engage
**Short term:** Proceed with the resize-not-crop decision. Better than cropping and doesn't require ML classification.

**Medium term (flag for Shawn):** LinkedIn's document carousel post type solved the infographic problem structurally, not at the rendering layer. If infographic posts are a meaningful use case in Engage (tenant admins, HR, comms teams post these regularly), a carousel/document post type may be the right end state — not just a smarter rendering rule.

**If indi8 classification is pursued:** Scope carefully. The classification only needs to answer one binary question: is this image text-heavy? Even a simple heuristic (image contains >40% high-contrast text pixels) may be sufficient without full ML investment.

---

*Research completed March 3, 2026. Based on published platform specs, engineering blog analysis, and UX teardowns. Screenshots needed to validate visual rendering behavior — see screenshot list in plan notes.*
