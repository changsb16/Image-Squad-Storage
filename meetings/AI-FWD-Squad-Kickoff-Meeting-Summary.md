# AI FWD Squad Kickoff — Meeting Summary
**Advancing Media Experiences in Engage (Web + Mobile)**

**Date:** March 2, 2026
**Duration:** 58 minutes
**Recorded by:** David Chang

---

## Participants

| Name | Role |
|------|------|
| David Chang | PM — Media Experiences (meeting lead) |
| Anushka Munasinghe | Engineering Leadership |
| Sandra Martinez Vargas | Product / Design Leadership |
| Erin Rosenthal | Product |
| Peng Zhou | Engineering — Backend / Android |
| Subodh Nijsure | Engineering — Mobile (iOS + Android SME) |
| Richard Tagger | Engineering |
| Shawn | Design (out sick — not present) |

---

## Purpose & Goals

This crew is one of three **AI FWD pilot squads** within the Engage org. Unlike the other two (backend-focused Relocator and frontend-focused Communities IA), this is the **only full-stack squad** covering web and mobile together.

**Dual mission:**
1. **Ship improvements** to media experiences in Engage (image layout, video rendering, media post flow)
2. **Pioneer AI-first development processes** that can be scaled to the broader Engage org

The 7-week cycle **officially starts Monday, March 9**. FHL week (this week) is voluntary prep — no obligation to squad work.

---

## Key Discussion Points

### A. Squad Context & Mission

- Engineering has seen measurable gains from Claude + GitHub Copilot; the bottleneck has shifted to the PM/Design → Engineering handoff
- This squad has freedom to explore new tools, agentic development patterns, and reimagined workflows — even if it slows short-term delivery
- The goal is to set an example for the rest of the org, not just complete the project. As Anushka put it: *"If we just ship the feature and that's all, we have failed."*

---

### B. Work Streams

David walked through 7 work items for the 7-week cycle. Not all will be completed — sequencing and priority will be determined as the crew goes.

| # | Work Stream | Status / Notes |
|---|-------------|---------------|
| 1 | **Image collage layout** — center cropping, defined aspect ratios (hero 3:2, secondary 4:3) | Miriam has front-end running in a private deploy; already in progress |
| 2 | **Smart cropping** — ML-driven focal point detection | Backend-heavy; debated ROI vs. center crop; Peng to weigh in on feasibility |
| 3 | **Media First Post redesign** — currently undiscoverable, only triggers for single images in Storyline | Needs full flow redesign; spike with Shawn + design first |
| 4 | **Video aspect ratio** — portrait videos are forced into a landscape rectangle | Quick win candidate; minimal design work needed |
| 5 | **Image gallery viewer** — viewing images while seeing thread comments and reactions | LinkedIn/Facebook parity; important for engagement; needs design spike |
| 6 | **Hero image selection** — let the post author choose which image is featured | Clean UX win; mid-sprint target |
| 7 | **Communities parity** | Several media features exist in Storyline but not Communities |

**On smart cropping:** Prior attempts at center cropping received customer pushback (faces/text getting cut off). Erin noted this is less urgent than other improvements; smart cropping remains an option but isn't the immediate focus. Roll out all changes behind an experiment flag.

---

### C. Mobile Considerations

- **iOS/Android parity is more important than mobile/web parity** — tenant admins need to coach users consistently across platforms; if a feature exists on iOS but not Android, that's a support burden
- **Consumption experience on mobile is the priority** — customers say they're competing with TikTok for employee attention; rich, engaging media on mobile is critical
- Mobile layouts will differ from web (pixel density, portrait/landscape, touch gestures) — this is accepted
- Subodh is the crew's mobile SME covering both iOS and Android
- Autonomous dev box execution works for Android; iOS builds require a Mac server to run 24/7

---

### D. PM/Design → Eng Handoff Model

Current workflow David used for image collage:
1. Build prototype and write a **markdown spec file** (acts as both spec and Claude prompt)
2. Publish to **GitHub Pages**
3. Share markdown + prototype with engineering (Miriam)
4. Iterate collaboratively — Miriam checked in changes directly to the prototype

**Key questions for this squad:**
- Is markdown the right handoff format, and what structure makes it most useful for engineers?
- Should engineering be involved earlier (even in the exploratory stage) to shape realistic constraints?
- Is GitHub Pages the right medium, or is something better available?

The communities IA squad is exploring **Figma MCP connectivity** — this squad will try a different path (markdown/GitHub) so both experiments can inform the org.

---

### E. AI-First Development Practices

- **Pressure-test options in parallel:** Rather than picking one design direction, use AI to rapidly prototype and evaluate multiple alternatives at once
- **Competitor intelligence:** Use LLMs to evaluate how LinkedIn, Instagram, TikTok, and others handle media layout — then compare to Engage. Peng suggested feeding screenshots + descriptions to models and asking for evaluation
- **Agentic multi-repo development:** Feed spec docs + competitive analysis into AI tools to generate implementation plans that span multiple repos simultaneously
- **Autonomous execution:** Peng is building toward 24/7 dev box agent execution — close laptop, let the agent keep building

---

### F. Way of Working

- **Daily crew hour:** David will pin a 2–4 PM Pacific time block in the group chat and use Meet Now. Join if you want; you'll be @mentioned when your input is needed
- **No formal daily standup** — shorter, more frequent touchpoints preferred over long ceremonial ones
- **Record all sessions** — reduces the cost of catching up later
- **Group chat is the official async channel** — David will not create additional channels
- The squad is intentionally **less scripted than traditional squad cycles** — the crew defines its own process and can reimagine any stage of the SDLC

---

### G. Feedback Loops & Last Mile

- **Shift UX review left:** Loop in the Lt. (UX review team) at the exploratory prototype stage, not after implementation. Current practice of waiting until coding prototype stage is too late
- **Get test suites in place** — identified as a long-standing gap
- **Use AI to accelerate last-mile tasks:** accessibility reviews, privacy/security checks, customer-facing communications, changelog writing
- **Early customer feedback:** Leverage existing flights/experiments infrastructure to expose features to select users. No EU customer data should be shared directly in emails or file shares

---

## Decisions Made

| Decision | Detail |
|----------|--------|
| 7-week clock starts March 9 | FHL week is voluntary prep; no obligation |
| Center cropping is the immediate approach | Not smart cropping; ship behind an experiment flag |
| Markdown + GitHub Pages as prototype handoff | Crew will explore this vs. Figma (communities IA tries Figma) |
| Group chat = official async channel | David will not create other channels |
| Less scripted than traditional squad cycles | Crew defines its own way of working |

---

## Action Items

| Owner | Action | Notes |
|-------|--------|-------|
| **David** | Pin daily crew hour (2–4 PM PT) in group chat; use Meet Now | Record all sessions |
| **David** | Document process improvement goals beyond shipping features | Capture Anushka/Erin/Sandra's framing from this call |
| **David** | Spike on media post flow redesign with Shawn | New flow for all images, not just single-image Storyline posts |
| **David** | Create competitor intelligence doc (LinkedIn, FB, TikTok vs. Engage media) | Feed into AI for pressure testing; Peng willing to help |
| **David** | Convert image collage spec into shareable markdown; distribute to crew | Share existing prototype as a reference |
| **David** | Join Pro Fund PMT virtual team for report-outs | Per Sandra's request |
| **David** | Identify customers willing to test early (non-EU only) | For prototype feedback during development |
| **Shawn** | Co-spike on media post flow redesign | Out sick this week; back soon |
| **Miriam** | Continue front-end image collage work in private deploy | Already in progress |
| **Peng** | Explore autonomous 24/7 dev box setup | Hackathon project this week |
| **Peng** | Run competitor intelligence analysis using LLMs | Evaluate media layout/video rendering across platforms |
| **Subodh** | Serve as mobile SME (iOS + Android) for the crew | — |
| **Anushka** | Confirm bandwidth allocation with Riley (Subodh) and Steve (Richard) | Richard has other commitments |
| **Eng team** | Investigate secure early-access prototype environment for customers | Existing flights/experiments may be sufficient |
| **All** | Use AI for test suite generation, accessibility, and privacy checks | Part of last-mile acceleration goal |
| **All** | No sharing of EU customer data in emails or direct file shares | Compliance reminder from Sandra |

---

*Summary written from meeting recording transcript — March 2, 2026*
