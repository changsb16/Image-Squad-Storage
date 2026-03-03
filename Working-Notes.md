# Working Notes — AI FWD Media Experiences Crew

Running feed of discussions, explorations, and things being considered.
Paste Teams chat snippets or summaries here → Claude will parse and organize.

---

## Week 1 — March 9, 2026

### Portrait Video Rendering
**Root cause confirmed:** Backend returns `videoWidth` and `videoHeight` as `null` for `SharePointVideoLink` media type. Portrait rendering code path exists and works — it just never gets hit because there's no dimension data. Richard hard-coded dimensions locally and confirmed it renders correctly when the data is present.

**Two separate tracks identified:**
- **Track 1 (backend fix):** Get the backend to return correct dimension info → portrait rendering works as designed with minimal black border. Richard owns this once there's a clear path.
- **Track 2 (card layout redesign):** David wants to go further — adaptive TikTok-style card layout for portrait videos, not just the dimension bug fix. This needs a design pass first. Richard confirmed he can implement once there's a blessed design.

**Open questions:**
- Who owns the backend dimension fix and what's the timeline?
- What does the TikTok-style card design actually look like? David + Shawn to spike.

---

### Mobile Private Deploy (Image Collage)
Miriam has a private deploy live with mobile changes per the markdown spec. Subodh couldn't load it — will DM Miriam to troubleshoot the sequence.

---

### Image Collage — Test Image Set
Peng asked whether there's a gold set of test images for collage algorithm evaluation. David has a local folder of real Engage photos he's been using manually. Peng flagged [PhotoCollage](https://github.com/adrienverge/PhotoCollage) — a well-known open source Python package for image collaging — as a potential tool, noting it could likely be ported to another language.

**Leaning toward:** Using David's existing local photo set as the baseline; evaluate PhotoCollage or similar as part of the collage algorithm spike.

**Open questions:**
- David's message got cut off — he was describing his local photo folder. What was the rest? *(finish sharing this in the next chat paste)*
- Is PhotoCollage worth evaluating vs. building our own? Peng to assess.

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
