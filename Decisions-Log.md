# Decisions Log — AI FWD Media Experiences Crew

Clean record of finalized decisions only. Updated by Claude as decisions are confirmed from working notes or chat.

---

## Kickoff — March 2, 2026

| Date | Decision | Rationale | Owner |
|------|----------|-----------|-------|
| Mar 2 | 7-week cycle starts officially March 9; FHL week is voluntary prep | FHL is not an obligation for squad work | Anushka |
| Mar 2 | Center cropping (not smart cropping) as the immediate starting point | Smart cropping is ML-driven and backend-heavy; not worth the risk for week 1 | David / Peng |
| Mar 2 | Ship all changes behind an experiment flag | Allows controlled rollout and measurement; previous center crop attempt got customer pushback | David |
| Mar 2 | Markdown + GitHub Pages as the prototype handoff format (vs. Figma) | Communities IA crew tries Figma; this crew tries markdown-first so both experiments inform the org | David |
| Mar 2 | Group chat is the official async communication channel | No additional channels will be created | David |
| Mar 2 | iOS/Android parity prioritized over mobile/web parity | Tenant admins need to coach users consistently across platforms; this is the higher-value gap | Anushka / Erin |

---

## Week 1 — March 9, 2026

| Date | Decision | Rationale | Owner |
|------|----------|-----------|-------|
| Mar 9 | Portrait video fix = two separate tracks: (1) backend dimension fix, (2) card layout redesign | Dimension fix gets to "works as designed"; TikTok-style layout is a separate design + eng pass | David / Richard |
| Mar 9 | Card layout redesign needs a blessed design before eng work begins | Richard confirmed he can implement once design is ready — no eng work starts without it | David (design spike w/ Shawn) |
| Mar 9 | Joshua owns the SharePointVideoLink dimension fix in Filelink service | Root cause confirmed: dimensions fetched from Graph but dropped during serialization; systemic across all URL-shared videos | Joshua |
| Mar 9 | LinkedIn and Facebook are the first targets for competitor intelligence reverse engineering | David confirmed; approach: dump site → ask Claude to generate system design, apply to media card layout | Peng |
| Mar 9 | Text/infographic images treated differently — resize instead of crop | Cropping chops text; resizing is imperfect but less destructive; classify at indi8 index time to identify text-heavy images | David / Peng |
| Mar 9 | Test image set to be standardized in a shared folder | Need diversity: varied dimensions, quality, categories, and faces for cropping validation; Peng seeded with 170 images | David (defines gold standard criteria) |

---

## FHL Week — March 4, 2026

| Date | Decision | Rationale | Owner |
|------|----------|-----------|-------|
| Mar 4 | +N overflow count uses accurate count — greyed image is treated as "shown," not hidden | Models after LinkedIn (not Facebook); two-way door, will revisit with engagement data if needed | David / Meerim |
| Mar 4 | Ship current image collage implementation (center-alignment, no face detection) first behind a flight | Reduce risk; layer face detection as a separate experiment afterward | Meerim / David |
| Mar 4 | Face centering is client-side; identity recognition is backend but out of scope | Aspect ratios are device-dependent; centering must happen on-device | Subodh |
| Mar 4 | Lazy backfill for video dimensions — no need to backfill old/ancient videos | ~1s p99 latency is acceptable; only triggers once per video | Joshua |
| Mar 4 | Collect real customer examples of problematic text/infographic images before building any classification | Screenshots and logos complicate classification; gather evidence before committing | David |

<!-- New decisions get added below as they're made, grouped by week -->
