# AI FWD Media Experiences — Weekly Update (Week of March 10, 2026)

**Squad:** AI FWD — Advancing Media Experiences in Engage (Web + Mobile)
**Week:** March 10, 2026 (Week 1 of 7)

---

## Summary

The AI FWD Media Experiences squad officially kicked off its 7-week cycle this week. We're improving how images and videos are created, displayed, and experienced in Engage — making the platform more visually engaging, fixing longstanding rendering bugs, and closing the gap with consumer platforms like LinkedIn and Facebook.

The biggest near-term milestone: Meerim's image collage work for web (PR in review with Core UI) and Subodh's mobile implementation (PR #265283 with ML Kit face-aware smart cropping) are both close to shipping behind a flight. This is the first substantive change to how images are laid out in Engage in years. Separately, Peng is researching the next layer — evaluating bin packing algorithms (Google Photos-style) and ML-driven smart cropping options — to be ready as a follow-on experiment once the baseline ships.

This week also saw a rapid prototyping breakthrough: Shawn built a working "Media Immersive View" — a universal side-panel showing image, carousel, thread content, comments, and reactions together — in a single day using Claude Code. The prototype is ready to hand off to front-end engineering. We're using this project as a live test of AI-native PM/Design/Engineering workflows, and the throughput so far speaks for itself.


---

## What Are We Building

Engage's media experience has lagged consumer platforms for years. Images get cropped poorly, portrait videos render as broken landscape boxes, and the experience of viewing images in context (with comments and reactions) doesn't exist unless you happen to post in a specific way. Most users don't even know these features exist.

We're fixing this across five parallel work streams over 7 weeks:

| Work Stream | What Changes | Status |
|-------------|--------------|--------|
| **Image Collage** | Adaptive layout with defined aspect ratios; center-cropping then face-aware smart cropping as follow-on | Meerim: PR in review with Core UI. Subodh: PR #265283 up (ML Kit face-aware). Targeting Engage dogfood / MSIT. |
| **Media Immersive View** | Universal side-panel for all image clicks — preview + carousel + thread content + comments + reactions | Prototype complete (Shawn). Handoff to Meerim / Richard this week. Scorecard experiment next. |
| **Portrait Video Fix** | Backend dimension fix for SharePointVideoLink + aspect ratio control via One Player | Joshua's fix mostly in. Working on "every load" fix. One Player team conversation pending. |
| **Smart Cropping** | ML-driven focal point detection as a separate experiment layer on top of center-crop | Peng's bin packing demo built. Subodh's face-aware PR up. Ships after center-crop lands. |
| **Media Post Flow Redesign** | Unified publisher — remove mandatory image position choice, default to top | David + Shawn spike planned. Usage data review first. |

---

## Current Progress

**Media Immersive View (Shawn)**
- Shawn used Claude Code to find the existing media-first post immersive component and apply it universally across all post types — any image click now opens the immersive side-panel
- Added carousel navigation for multi-image posts (was missing entirely for non-media-first posts)
- Added download button for single-image view (was inconsistently absent)
- Comments are thread-level, consistent with how Facebook handles this — users see full post context
- Branch ready for handoff: `shawndeng/feat/media-immersive-view` (`git fetch origin && git checkout shawndeng/feat/media-immersive-view && npm run start:web`)
- Known gaps: carousel keyboard navigation not yet implemented; carousel aspect ratio is close but not pixel-perfect

**Image Collage (Meerim + Subodh)**
- Meerim has integrated David's prototype and has a PR in review with Core UI
- Subodh's PR #265283 is up: "Adaptive image carousel layout with ML Kit face-aware smart cropping"
- Experiment structure confirmed: separate experiments for web vs mobile; center-cropping and smart-cropping separated so they can be independently measured
- Subodh created iOS and Android experiments; cross-platform rollout is hard with new ECS — each platform needs its own experiment

**Portrait Video (Joshua)**
- Dimension fix is mostly in, but `head` and `width` still return null for the first user on the first feed load
- Next step: fix so it works for every load, not just subsequent ones
- After that: chat with One Player team to expose aspect ratio control — precedent is Albert, who previously made One Player code changes with their permission and review

**Algorithm Research (Peng)**
- Built a working Google Photos-style bin packing demo (no ML yet) — tested and posting to Engage
- Indexed android, iOS, yammer-clients, and search repos simultaneously with Claude Code; got solid multi-repo summaries with no hallucinations; surfaced that mobile has significantly more media logic than web

---

## Timeline & Rollout

| Target | Milestone |
|--------|-----------|
| End of April | Image collage (center crop) behind a flight → Engage dogfood or MSIT, scorecard live |
| End of April | Portrait video dimension fix shipped; One Player aspect ratio conversation started |
| Follow-on | Smart cropping as a separate experiment on top of Meerim's center-crop work |
| Follow-on | Media Immersive View behind a scorecard experiment to measure engagement impact |
| In parallel | Media post flow redesign spike (David + Shawn) — complexity TBD |

**Experiment concern:** We're planning to run several experiments simultaneously. May need a data scientist (Peng or external DS) to ensure they're not confounded and that we can attribute engagement changes correctly.

---

## How We're Working

**Parallel workflows — don't wait for handoffs.** Shawn's prototype is done; Meerim and Richard can now pick it up while Shawn and David move to the next problem (media post flow redesign). We're cascading, not sequencing.

**Ship the prototype, hand off the branch.** Shawn's approach this week was the model: spike with Claude Code, get something working locally, share the branch. Front-end engineers don't need a Figma spec — they can read the code and run it. This cut the design-to-engineering handoff to near zero.

**Experiment everything.** Every change goes behind a flight and scorecard. Media post usage is low (~low single digits) and likely accidental — we need data, not intuition, to justify each design direction.

**AI tools as the primary dev environment.** Shawn coded the entire prototype via Claude Code prompting. Peng indexed four repos in an evening. The bottleneck is no longer "can we build it" — it's "what should we build."

**Daily touchpoints over formal standups.** David uses a pinned group chat hour (2–4 PM PT). Short async updates in chat replace long scheduled meetings. Sessions are recorded.

---

## AI: What We're Doing Today

| Area | How AI is Being Used |
|------|----------------------|
| Rapid prototyping | Shawn described desired behavior to Claude Code → working prototype in hours. No prior deep codebase knowledge required. |
| Multi-repo codebase indexing | Peng indexed android, iOS, yammer-clients, and search repos with Claude Code in one session — got accurate summaries, surfaced mobile-specific logic gaps |
| Spec generation | PM writes requirements context; Claude generates specs. Working notes → decisions log → weekly updates all AI-assisted (this update included) |
| Competitor intelligence | Fed competitor screenshots + designs to Claude to reverse-engineer layout logic; applying to Engage media card design |
| Meeting transcript → working notes | Teams transcripts processed directly by Claude into structured working notes and decisions log entries |

---

## What We're Learning

- **Prototyping with Claude Code is faster than writing a spec.** Shawn's immersive view prototype took one day. The output is more useful than a Figma frame because engineers can just run it. The new question is whether PM/Design even needs to write specs first, or if we spike → spec → hand off.
- **Media post usage is low and mostly accidental.** The feature exists but isn't discoverable. Engagement improvements from better UX may be large — but we need an experiment to confirm, not assume.
- **Carousel keyboard navigation is a gap.** Whenever we add new interactive components, we need to audit accessibility from the start — not after the fact. Shawn flagged it; adding it as a standard checklist item going forward.
- **Experiment separation matters.** Center-crop and smart-cropping need separate experiments. Web and mobile need separate experiments. Running these as one would make it impossible to attribute engagement changes correctly.

---

## What We're Exploring

| Opportunity | What We're Testing |
|-------------|-------------------|
| Unified media post flow | If the Media Immersive View makes all image previews look the same, do we still need to fork the experience (media-first vs. inline)? Default to top, remove mandatory publisher choice. Reviewing usage data first. |
| Bin packing vs. custom collage layout | Peng's Google Photos-style bin packing demo is working. Evaluating vs. current adaptive grid approach — which handles extreme aspect ratios better? |
| Meeting transcript → spec pipeline | We have transcripts from all squad meetings. Could auto-extract decisions and open questions, append to living working notes. This update was generated that way. |
| Server-side face detection | Joshua's chat log note: run face detection on the backend at upload time (Azure AI Vision), store bounding boxes in file metadata, let the client compute crop locally. Feasibility and cost TBD. |
| One Player aspect ratio API | Can the One Player team expose an API to control aspect ratio? Albert precedent suggests yes — we code it with their review. |

---

## Team

| Role | People |
|------|--------|
| PM | David Chang |
| Design / AI Prototyping | Shawn Deng |
| Engineering — Web (FE) | Meerim Oboskanova, Richard Tagger |
| Engineering — Mobile (iOS + Android) | Subodh Nijsure |
| Engineering — Backend / Android / DS | Peng Zhou |
| Engineering — Backend | Joshua Bae |
| Engineering Leadership | Anushka Munasinghe |

---

## Open Items

- [ ] Meerim / Richard to check out `shawndeng/feat/media-immersive-view` and begin productionization review
- [ ] Shawn to prompt Claude for carousel keyboard accessibility fix
- [ ] Joshua to complete "every load" fix for portrait video dimensions
- [ ] David to set up One Player team conversation (Joshua + David)
- [ ] David to share Media First post usage dashboard with Shawn; schedule sync to review data + spike on unified media post flow
- [ ] Determine DS involvement for simultaneous experiment coordination (Peng or external DS)
- [ ] Confirm whether Joe's download removal guidance affects images or files only — Joshua to follow up with Joe to close the loop
- [ ] David to tag Nooper and Richard on Shawn's Media Immersive View prototype for async review
