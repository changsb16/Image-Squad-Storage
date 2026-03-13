# Engage Post Draft — Week of March 12, 2026

---

**Week 1 update from the AI FWD Media Experiences crew — and things are moving fast.**

We're a 7-week squad pushing hard to advance how media works in Viva Engage — better image layouts, portrait videos that actually render correctly, a richer image viewing experience, and a reimagined end-to-end media flow. Here's where we are and how we're working.

---

**What we're building (and fixing) in parallel**

Five work streams are running simultaneously right now:

🖼️ **Image Collage** — Meerim and Subodh are flighting the first version: an adaptive grid layout with defined aspect ratios and center-cropping, rolling out to web and mobile separately so we can measure each independently. Meanwhile, Peng is already building V2 with face detection and dynamic aspect ratios — POC results are in and looking good. Sub-ms latency on iOS, ~100ms typical on Android.

👁️ **Media Immersive View** — Shawn prototyped a universal image viewer in a single day using Claude Code — any image click in Engage now opens a side-panel with image preview, carousel navigation, thread content, comments, and reactions. Richard has confirmed scope and is planning implementation. We're extending this to all post types, including Articles, behind a flight.

🎬 **Portrait Video** — Joshua has been tracking down why portrait videos render as broken landscape boxes. Fix is progressing, and we just surfaced a second related issue this week. One Player team conversation is next once the fix is in.

🔄 **Media Flow Redesign** — Shawn and I are rethinking the media experience end to end — from how you attach and compose with images in the publisher, to how your audience views and interacts with them after posting. We're prototyping first and will reverse-engineer a spec from the prototype once the direction is validated.

📐 **Platform Parity** — Subodh is socializing the image collage work with the iOS and Android teams this week, and what surfaced is a real gap: web and mobile decisions don't translate automatically. We named this a workstream explicitly so we can close it in parallel.

---

**Goals for the next 3 weeks**

Our goal this sprint is to close the gap between Engage and modern media platforms — fixing longstanding rendering issues, shipping a richer viewing experience, and reimagining the media flow end to end. Checkpoints for the next 3 weeks:

- Image collage V1 (center-crop) live in Engage dogfood/MSIT with a scorecard running
- Portrait video dimension fix shipped; Media Immersive View spec in Richard's hands
- V2 image collage branch started with face detection + dynamic layouts
- One Player team conversation started for aspect ratio control
- Privacy review engaged early for ML/face detection — not a last-minute gate

---

**How we're working differently — what we're learning about AI-native development**

This squad is as much about pioneering a new way of working as it is about shipping features. Here's what's standing out:

**Specs in hours, not days.** When engineering needs a spec to get started, we can have a structured markdown spec ready the same morning — sometimes within an hour or two of the ask. PM and Design use Claude to draft, structure, and refine in real time. What used to take days to write and align on now happens before lunch.

**Prototype first, spec always.** Our model: spike a prototype with Claude Code to get visual confirmation and test real edge cases in Engage — then reverse-engineer a markdown spec from what we learned. The prototype and the code aren't necessarily the final implementation (the Core UI components and logic will likely need rework), but they're the fastest way to validate a direction and surface what the spec needs to say. The visual confirmation plus the MD file is the handoff. Shawn's immersive view went from idea to working prototype in one day and a spec followed.

**Living docs over static deliverables.** Rather than a spec that gets written and forgotten, we keep a running decisions log and working notes — both as markdown files in our repo. Every team chat and meeting transcript feeds directly into them. Decisions stay current. Nothing gets lost in a chat thread. And because they're markdown, updating them takes seconds with AI assistance.

**Parallel by default.** We don't wait for one workstream to finish before starting the next. Shawn's prototype ships to Richard, and Shawn is already moving to the next problem. Meerim builds V1 while Peng builds V2. Cascading, not sequencing.

**AI removes the "stuck in analysis" trap.** When a question comes up — should we do face detection on the client or backend? What did LinkedIn ship here? What does our usage data actually say? — we can get to an answer fast enough to act on it the same day. The bottleneck is no longer information gathering. It's what to focus on and why, and then building and testing it rather than talking about it.

---

**Monday the crew is getting together to teach each other.** We're setting aside an hour for everyone to screen share and walk through how they're actually using AI tools day-to-day — not a presentation, just open time to shadow each other and pick up patterns that are working.

This is week 1 of 7. More to come.

— David and the AI FWD Media Crew

*(Meerim Oboskanova, Shawn Deng, Richard Tagger, Subodh Nijsure, Peng Zhou, Joshua Bae, Anushka Munasinghe)*
