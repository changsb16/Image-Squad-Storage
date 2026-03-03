# Working Notes — AI FWD Media Experiences Crew

Running feed of discussions, explorations, and things being considered.
Paste Teams chat snippets or summaries here → Claude will parse and organize.

---

## Week 1 — March 9, 2026

### Portrait Video Rendering
**Root cause confirmed (Richard + Joshua):** Backend returns `videoWidth` and `videoHeight` as `null` for `SharePointVideoLink` media type. Portrait rendering code path exists and works — it just never gets hit because there's no dimension data. Richard hard-coded dimensions locally and confirmed it renders correctly when the data is present.

**Systemic scope (Joshua):** This affects ALL SharePointVideoLinks (URL shares). VideoFile uploads (direct uploads) correctly return dimensions. Root cause is in the Filelink service — dimensions are fetched from Graph but dropped during serialization.

**Fix approach:** Joshua is investigating. Backfill is likely feasible — lazy-backfill on render when dimensions are null using the current user's AAD token, then update the record. Complication: Filelink needs an AAD token to do the Graph fetch.

**Two separate tracks:**
- **Track 1 (backend fix):** Joshua owns the Filelink serialization fix + backfill. Richard to follow up on timeline.
- **Track 2 (card layout redesign):** David wants adaptive TikTok-style card layout beyond the fix. Needs a blessed design first — David + Shawn to spike.

**Open questions:**
- Backfill approach confirmed feasible? Joshua still investigating.
- What does the TikTok-style card design actually look like? David + Shawn to spike.

---

### Mobile Private Deploy (Image Collage)
Miriam has a private deploy live with mobile changes per the markdown spec. Subodh couldn't load it — will DM Miriam to troubleshoot the sequence.

---

### Image Collage — Test Image Set
**Status:** A shared test image set is now taking shape.
- David has a local folder of real Engage photos (infographics, text, group photos, animals). Will define the "gold standard" criteria and create a shared folder for the crew.
- Peng downloaded ~100 images of varied dimensions and categories from free image services using Claude Code; uploaded 170 images to a shared SharePoint folder (link in Teams chat).
- Subodh: Android test community has layout test images but faces are rare. **Faces are important** for validating cropping and centering.
- Peng: Can also generate test images using AI tools if needed.
- Peng flagged [PhotoCollage](https://github.com/adrienverge/PhotoCollage) as a reference Python package for collage algorithms; likely portable to another language.
- Subodh's agent already implemented face detection (Google Face Detection ML library) from David's spec — before/after recording shared in chat.

**Leaning toward:** Shared folder with diverse images (varied dimensions, quality, and categories including faces). Gold standard criteria to be defined by David.

**Open questions:**
- Is PhotoCollage worth evaluating vs. building our own? Peng to assess.
- Where is the canonical shared test image folder? Peng's SharePoint folder for now?

---

### Competitor Intelligence — Reverse Engineering Approach
Peng flagged a technique: dump a website and ask Claude to generate a system design from it (LinkedIn post example used Netflix). Proposed applying this to Facebook and Instagram to understand their media layout and card design approaches.

**Leaning toward:** Start with LinkedIn and Facebook (David confirmed these are good targets).

---

### Image Collage — Text / Infographic Images
David (in conversation with Brendan): Images with text (infographics) should be treated differently — no level of cropping works well for them. Cropping chops off text.

**Options considered:**
- Resize instead of crop for text images
- Classify images at index time in indi8 to identify text-heavy images and handle differently

**Leaning toward:** Resize rather than crop for text images. May still upset some customers but better than chopping. Peng noted indi8 classification is possible.

**Open questions:**
- What does "resize" look like in the layout? Does it break the grid?
- Is indi8 classification realistic within the 7-week scope?

---

### Image Collage — Hero Image Selection
**Current state (confirmed):** Hero image = first image, on both iOS and Android. No way to change it on either platform currently.

**Desired state:** Let users select the hero image — already a listed work stream. On mobile, the native interaction (drag/reorder) would be the UX pattern to aim for.

**Open question (Subodh):** Should hero selection be ephemeral (per session) or persistent and consistent across platforms?

---

### Image Collage — Duplicate Images Bug
Peng found: multiple copies of the same image can be added easily. Likely unintended behavior.

**Status:** Flagged, not yet assigned.

---

<!-- TEMPLATE FOR EACH ENTRY:
### [Date]
**Exploring / Considering:**
-

**Leaning toward:**
-

**Open questions:**
-
-->
