# Working Notes — AI FWD Media Experiences Crew

Running feed of discussions, explorations, and things being considered.
Paste Teams chat snippets or summaries here → Claude will parse and organize.

---

## Week of March 10, 2026

### Media Immersive View — Shawn's Prototype

**What Shawn built:** Used Claude Code to find the media-first post immersive component and apply it universally to all post types. Any image click now opens a side-panel with: image preview + carousel + thread content + comments + reactions. Confirmed working locally.

**Key details:**
- Carousel navigation added for multi-image posts (the media-first post didn't have this; Shawn added it as an overlay on the image)
- Download button added for single-image view (it was inconsistently absent for single images)
- Comments shown are **thread-level**, not image-specific — consistent with how Facebook handles this; users will understand context from the thread content
- Carousel is not yet keyboard-accessible — Shawn continuing to prompt Claude for the fix
- Download button downloads only the currently-viewed image, not all images at once

**Branch:** `shawndeng/feat/media-immersive-view`
```
git fetch origin
git checkout shawndeng/feat/media-immersive-view
npm run start:web
```

**Handoff plan:** Miriam or Richard to review and productionize. David + Shawn continue in parallel on the media post flow redesign.

**Open questions:**
- Carousel keyboard navigation still needs work — Shawn prompting Claude
- Does the carousel aspect ratio need adjustment? (currently overlay on image, not perfect)

---

### Media Post Flow — Unified Experience Question

The success of Media Immersive View raises a bigger design question: if all image previews now look the same, why maintain a separate "media-first" vs "inline" post flow?

**Current state:** Publisher forces users to choose image position (above/below) — this is friction with unclear benefit.

**Shawn's take:** If all previews look the same regardless of position, why fork at all? Pick the best default.

**David's lean:** Default to top (better engagement signal from media team perspective), but let users reposition if they want. Don't block them in the publisher.

**Data:** David has a dashboard for Media First post usage — usage is low and likely accidental; mostly power users. Will share with Shawn. Dashboard: https://yammer.scuba.io/board/b1458

**Next step:** David + Shawn to sync (possibly tomorrow morning) to review data and prototype the unified flow (Figma or in-code prototype TBD).

---

### Portrait Video — One Player Track Update

**Joshua's status:** Dimension fix mostly in, but `head` and `width` still return null for first user on first feed load. Needs to work for every load, not just subsequent loads.

**After fix:** Team will need a formal chat with One Player team to expose aspect ratio control. Precedent: Albert previously made code changes in One Player codebase with their permission and review — same model applies here.

**Open questions:**
- Timeline for Joshua's "every load" fix?
- Who sets up the One Player team conversation?

---

### Experiment Strategy — April Goals

David's summary for broader team / async stakeholders:

1. **Image collage (center crop)** — Miriam + Subodh work → behind a flight → ship to Engage dogfood or MSIT → scorecard running
2. **Smart cropping** — separate flight/experiment on top of Miriam's current work
3. **Portrait video** — separate track (Joshua's fix + One Player conversation)
4. **Media Immersive View** — scorecard experiment to measure engagement difference
5. **Media post flow redesign** — David + Shawn spike in parallel; complexity TBD

**Experiment coordination concern:** Running many experiments simultaneously. May need DS involvement (Peng or external DS) to ensure they're not confounded.

**Experiment structure confirmed:**
- Separate experiments: web vs mobile for image collage
- Separate experiments: Miriam's center-cropping vs Peng's smart-cropping
- Subodh created iOS and Android experiments; cross-platform rollout is hard with new ECS

---

### Image Collage — Engineering Progress

- **Subodh:** PR #265283 up — "Adaptive image carousel layout with ML Kit face-aware smart cropping"
- **Meerim:** Integrated David's prototype; PR in review with Core UI
- **Peng:** Built a Google Photos-style bin packing demo (no ML yet) — works well. Tested posting to Engage; also indexed android, iOS, yammer-clients, and search repos with Claude Code for a multi-repo summary (solid output, no hallucinations, surfaced mobile has more logic)
- **Meerim asked Subodh:** Reuse the same image collage experiment or create separate ones? → David's answer: separate (web vs mobile, center-crop vs smart-crop)

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

---

## FHL Week — March 4, 2026

### Image Collage — +N Overflow Count
Team aligned on how to count the overflow number shown on the last greyed slot in the collage preview.

**Two approaches considered:**
- **Accurate count (Approach A):** Greyed image is "shown" — e.g., 7 images in 4-slot collage → `+3`
- **Inflated count (Approach B):** Greyed image is "hidden" — same scenario → `+4` (Facebook's approach)

David confirmed: modeling after LinkedIn, which uses accurate counts. Meerim noted it's a two-way door — can revisit with engagement data if needed.

**Decided:** Accurate count. Greyed image is treated as shown, not hidden. Mobile already updated to match.

---

### Image Collage — Face Detection Placement
Discussion on where face detection logic should live.

- **Client-side for centering:** Subodh confirmed face centering must happen client-side because aspect ratios depend on the device
- **Backend for identity:** That would be backend — but we're doing centering only, not identification; identity recognition is out of scope
- **APK size:** Face detection increases Android APK size by ~2% — acceptable for Engage, but Teams integration needs consideration (Subodh)

**Leaning toward / decided:** Ship current implementation (center-alignment without face detection) first, behind a flight. Layer face detection as a separate experiment later.

**Open questions:**
- Does Teams integration have APK size constraints that would affect face detection?

---

### Image Collage — Face Detection on Web
Web today uses center-alignment with existing rules. No face recognition mechanism exists on web.

- Peng flagged lightweight JS options: **face-api.js** and other browser-based face detectors
- Meerim raised concern: third-party libraries inside Microsoft require an OSS approval process (register the library, go through compliance + security checks per Peng)
- Current plan: align web and mobile behavior first; web face detection is a potential follow-on step

**Open questions:**
- Is face-api.js approvable through Microsoft's OSS process? Peng to assess if this becomes a next step.
- What specifically needs to change on web to align with mobile center-alignment behavior?

---

### Portrait Video — Lazy Backfill Update
Joshua confirmed acceptable performance for the lazy backfill approach:
- Latency: ~1s p99 — happens once per video, then the record is updated
- David: no need to backfill old/ancient videos — only on-demand when dimensions are null

---

### Customer Feedback Timing
David: can't wait weeks for customer feedback on image collage. Need early signal.

**Plan (Meerim confirmed feasible):**
1. Show customers the current center-crop prototype first (no face detection)
2. Show face-aware version as a follow-on once that experiment is ready

**Open question:** David to identify specific customers willing to test early (non-EU only per Sandra's compliance reminder).

---

### Image Collage — Text/Infographic Classification
Peng: classification could help identify text-heavy images, but screenshots and logos complicate things.

**David's call:** Don't over-engineer this yet. Collect real customer examples of problematic text/infographic images first before committing to a classification approach.

**Status:** Gathering customer examples is the next step before any implementation decision.

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
