# Problem Statement — Advancing Media Experiences in Engage (Web + Mobile)

---

## The Product Problem

Engage's media experience is not consumer-grade, and it shows. Images render with grey blurred borders, portrait videos are forced into landscape rectangles, and aspect ratios are inconsistently applied across the product. The result is a feed that looks unpolished compared to the platforms our customers' employees are already using every day.

Beyond rendering quality, core media features are broken at a UX level:

- **Media First Post** — a feature designed to spotlight images — is effectively invisible. It only surfaces after a user hits post with a single image attached in Storyline, is unavailable in Communities, and sees only tens of uses per month. Most users don't know it exists.
- **Image gallery viewing** doesn't let users see thread comments and reactions alongside images — a standard interaction pattern on LinkedIn, Facebook, and TikTok that Engage currently lacks.
- **Mobile is the worst offender.** Frontline workers consume Engage primarily on their phones. A degraded media experience on mobile means we're competing with TikTok for their attention — and losing. Tenant admins compound the problem: iOS/Android feature parity gaps make it difficult to confidently coach users, eroding trust in the platform.

---

## The Process Problem

Engineering is measurably faster thanks to AI tools (Claude, GitHub Copilot). That's progress — but it's exposed a new bottleneck: the handoff between PM/Design and Engineering.

Specs and prototypes today aren't structured in ways that engineers or AI agents can act on directly. Work gets rebuilt from scratch. Context is lost at each handoff. Late-stage reviews — accessibility, trust & privacy, experience/UX — arrive as surprises rather than being built into the workflow.

This crew is a pilot to rethink that entire handoff:

- **Markdown-first specs** that function as both product requirements and AI prompts, so engineering can move immediately without re-interpretation
- **AI-assisted parallel exploration** — using AI to rapidly pressure-test multiple design directions, competitor approaches, and user flows simultaneously, enabling faster decisions without sequential design review cycles
- **Shifting gates left** — accessibility, trust & privacy, and experience review shouldn't be last-mile blockers; we'll explore how AI tooling and earlier team involvement can catch these issues during the prototype stage, not after implementation

---

## Who Is Affected

| Audience | Pain |
|----------|------|
| Frontline workers (FLW) | Poor mobile media rendering reduces engagement and competes with consumer social apps |
| Tenant admins | iOS/Android parity gaps make it hard to train and support users at scale |
| Content creators / communicators | A confusing, undiscoverable media post flow limits how effectively they can communicate |

---

## What Success Looks Like

### 3.5-Week Goals (by ~March 27)

| Goal | Detail |
|------|--------|
| Image collage (center crop) behind flights — web + mobile | Meerim's web PR and Subodh's mobile PR merged behind experiment flags; MSIT scorecard instrumentation set up and ready to run |
| Portrait video fix behind a flight | Joshua's dimension fix shipped for all load scenarios; experiment flag in place; MSIT scorecard ready |
| Media Immersive View behind a flight | Shawn's prototype productionized by Meerim / Richard; behind experiment flag; MSIT scorecard ready |
| Customer feedback collected | Early customer signal gathered ahead of accessibility and TRUST reviews — non-EU customers only per compliance |
| Accessibility review started | Accessibility assessment initiated on all three shipped experiences; keyboard navigation and screen reader gaps identified and tracked |
| TRUST review discussions started | Initial TRUST / privacy review conversations underway — not blocked, but started early enough to avoid late-stage surprises |

### 7-Week Goals (by ~April 21)

| Goal | Detail |
|------|--------|
| Smart cropping ready for MSIT experimentation | ML-driven focal point / face-aware cropping implemented as a separate experiment layer on top of center-crop baseline; scorecard running at MSIT |
| Media First Post end-to-end flow prototyped and ready for dogfooding | Unified publisher flow (default image position, no mandatory choice) prototyped and validated; ready for Engage dogfood or MSIT-level testing |
| Engagement signal from all three 3.5-week experiments | Image collage, portrait video, and Media Immersive View scorecards have initial data; directional read on engagement impact |

### Process Outcomes (learnings to share with the org)
- A documented, reusable markdown-first handoff format that engineers and AI agents can act on directly
- A demonstrated pattern for AI-assisted parallel design exploration — faster decisions, fewer late pivots
- A playbook for shifting accessibility, trust & privacy, and UX experience reviews earlier in the cycle
- Published findings that other Engage feature crews can adopt immediately
