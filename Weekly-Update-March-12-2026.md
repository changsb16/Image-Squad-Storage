# AI FWD Media Experiences — Weekly Update (Week of March 12, 2026)

**Squad:** AI FWD — Advancing Media Experiences in Engage (Web + Mobile)
**Week:** March 12, 2026 (Week 2 of 7)

---

## Summary

Week 2 is where the squad goes from aligned to executing in parallel. Five work streams are active simultaneously — image collage is in the flighting phase, the Media Immersive View prototype is being handed off to engineering, portrait video has a new open question, and V2 smart cropping POC results are in. Separately, we're formalizing how the squad operates, with a Monday session to share AI tooling learnings across the team and early customer outreach for the first image collage flight.

The biggest news this week: Richard confirmed alignment on the Media Immersive View scope — carousel added to media posts, extended to all post types including Articles, both behind flights — and Meerim confirmed V1 image collage is built exactly per the MD spec. 

---

## What Are We Building

| Work Stream | What Changes | Status |
|-------------|--------------|--------|
| **Image Collage — V1** | Adaptive layout with center-cropping; web hero + 3 secondaries, mobile hero + 2 | Meerim: V1 per spec, private deploy live. Subodh: socializing with iOS/Android teams. Flighting in progress. |
| **Image Collage — V2** | Face detection + dynamic aspect ratios as a separate experiment layer | Peng's face detection POC showing good results (50–300ms latency). Starting separate V2 branch. Meerim + Subodh to cross-train. |
| **Media Immersive View** | Universal side-panel for all image clicks — preview + carousel + thread + comments + reactions | Scope confirmed with Richard. David + Shawn delivering MD spec. Richard begins planning; implementation follows spec. |
| **Portrait Video Fix** | Backend dimension fix for SharePointVideoLink + new finding: Storyline VideoFile portrait rendering also broken | Joshua: SharePointVideoLink fix progressing. New issue surfaced: VideoFile attachments not using portrait rendering despite having valid dimensions. |
| **Media Post Flow Redesign** | Unified publisher — remove mandatory image position choice, default to top, explore user-selectable hero | David + Shawn spike underway. Usage data reviewed. MD spec to follow prototype. |

---

## Current Progress

**Image Collage — V1 (Meerim + Subodh)**
- Meerim: V1 developed exactly per the MD spec. Private deploy available for Subodh; not yet in general MSIT. Working to flight to broader audience.
- Subodh: Socializing the work with iOS and Android teams this week. Pointed teams to [Problem-Statement.md](Problem-Statement.md) as the customer problem reference.
- Layout spec finalized: **web = hero + 3 secondaries** (last greyed with +# overlay); **mobile = hero + 2 secondaries**. Subodh flagged this should be captured formally in the spec — to do.
- Separate experiments confirmed: web vs. mobile; center-crop vs. smart-crop — independently controlled.
- New workstream added: **platform parity (Android / iOS / Web)** — surfaced as a gap during socialization.

**Image Collage — V2 POC (Peng)**
- Face detection POC: V2 layout working much better. Some face cropping still visible but acceptable — avoids the weird stretching from unconstrained aspect ratios.
- Latency: ~50–300ms. Not perfect; doesn't detect animal faces.
- If zero head cropping is required, slot aspect ratios need to be relaxed — tradeoff to watch.
- **Mobile performance (Subodh):** iOS Vision framework = zero binary size increase, sub-ms latency. Android ML Kit = ~0.5MB APK increase, 300–600ms first detection (model download), 50–150ms subsequent. Users likely won't notice.
- Meerim's flag: Engage has many multi-face photos — must work well across those before claiming parity.
- **Privacy review:** Peng flagged early engagement with Viva Engage privacy team is required for ML enhancements. David agreed — not a last-minute gate. Check-in before next Tuesday.

**Media Immersive View (Richard + Shawn)**
- Richard confirmed scope alignment (before going out sick today):
  - Carousel added to media posts (currently missing for media posts too)
  - Media Immersive View extended to all post types, including Articles
  - Both behind a flight
- David + Shawn owe a MD spec to Richard. Richard is planning with current context; implementation begins once spec lands.
- CoreUI component: Shawn checked Storybook — Claude used what was shown but correct component TBD. Meerim to investigate after image collage lands.

**Portrait Video (Joshua)**
- **New issue surfaced:** Storyline VideoFile attachments (uploaded from device) don't use portrait rendering even though `videoHeight` / `videoWidth` are correctly returned. This is separate from the SharePointVideoLink null-dimensions bug.
- Open question: Is portrait rendering intentionally unsupported for VideoFile type? Does this change rendering in the publisher too?
- Joshua to share test tenant creds/link with Richard to repro.
- One Player conversation still pending — after dimension fix is complete.

**Publisher / Media Post Flow**
- WYSIWYG preservation confirmed as a requirement: publisher must show what will actually be published when images are attached.
- Deeper publisher + editing testing flagged as needed (Meerim + Shawn).
- User-selectable hero image added to backlog for exploration.

---

## Goals: Next 3 Weeks (by end of March / early April)

| Target | Goal |
|--------|------|
| **End of March** | Image collage V1 (center-crop) behind a flight → Engage dogfood / MSIT; scorecard live |
| **End of March** | Portrait video SharePointVideoLink dimension fix shipped; VideoFile portrait rendering investigated |
| **End of March** | David + Shawn MD spec for Media Immersive View delivered to Richard |
| **Early April** | Media Immersive View implementation underway (Richard); behind a flight |
| **Early April** | Image collage V2 (face detection) branch started; Meerim + Subodh cross-training with Peng |
| **Early April** | One Player team conversation started for aspect ratio API |
| **In parallel** | DS support engaged for simultaneous experiment coordination |
| **In parallel** | Privacy review engaged for ML/face detection features |

---

## How We're Working

**PM + Design cascade into Engineering without waiting.** Shawn's prototype branch ships to Richard while Shawn and David have already moved to the next problem. We don't wait for handoffs to finish — we overlap them.

**MD spec is the contract.** Every prototype — whether in Figma or code — gets reverse-engineered into a markdown spec that engineers can use as the source of truth. Meerim built V1 image collage exactly per the spec; nothing drifted. This is the model we're repeating.

**Spec turnaround is now hours, not days.** When engineering needs a spec to start, PM can have a working MD spec ready the same morning — sometimes within an hour or two of the request. This is only possible because we're using AI to structure, draft, and refine in real time.

**Experiment everything.** Every change goes behind a flight. We're running web vs. mobile, center-crop vs. smart-crop, and immersive view engagement as separate experiments — independently controlled so we can actually attribute changes.

**AI session scheduled for Monday.** We're dedicating an hour to share how each person on the squad is using AI tools — what's working, what isn't, and what patterns might be worth spreading to the broader org.

---

## AI: What We're Doing This Week

| Area | How AI is Being Used |
|------|----------------------|
| Rapid prototyping | Shawn continues building and iterating on the immersive view prototype with Claude Code — branch ready for engineering handoff |
| Spec generation | Engineering requests a spec in the morning; PM has a structured MD spec ready within hours using Claude |
| Multi-repo indexing | Peng indexed android, iOS, yammer-clients, and search repos with Claude Code — solid summaries, surfaced mobile-specific logic gaps |
| Chat → working notes | Teams chat pasted directly into working notes; Claude parses and structures into decisions and open questions |
| V2 algorithm POC | Peng using Claude Code to build and test face detection + bin packing layout variants against the gold standard test image set |

---

## What We're Learning

- **The spec can follow the prototype, not precede it.** When Shawn builds something in code and it works, the right move is to reverse-engineer the spec from the prototype — not block the prototype on a spec. Engineers can run the branch; the spec validates and documents the intent.
- **MD-first handoffs remove ambiguity.** Meerim built V1 exactly per the spec with nothing drifting. When the spec is concrete and versioned, engineering doesn't have to interpret.
- **Face detection is fast enough on mobile.** Sub-ms on iOS and ~100ms typical on Android — we're not blocked on performance. Privacy review is the real gate.
- **Platform parity is its own workstream.** Surfaced during Subodh's iOS/Android socialization — you can't assume web and mobile decisions translate directly. Naming it explicitly unlocks parallel progress.
- **Running multiple experiments at the same time needs a DS.** We're about to have 4+ simultaneous flights. Without proper experiment design, we won't be able to attribute changes to the right cause. Getting ahead of this now.

---

## Open Items

- [ ] Meerim: flight V1 image collage; update spec with layout numbers (hero + 3/2 secondaries)
- [ ] Subodh: complete iOS/Android team socialization; start V2 cross-training with Peng
- [ ] Peng: start V2 branch; prep for privacy review before Tuesday
- [ ] Shawn: re-polish immersive view; complete MD spec for carousel + immersive view with David
- [ ] Richard: plan immersive view + carousel implementation; begin once MD spec lands
- [ ] Joshua: investigate Storyline VideoFile portrait rendering issue; share test tenant link with Richard
- [ ] David: deliver MD spec for immersive view with Shawn; reach out to customers who flagged image collage; host Monday AI tools session; request DS support; set up One Player conversation
