---
title: 5TH POST (Revisiting earlier assumptions)
date: 2026-05-15
author: MANASVI SINGH
Summary: "Revisiting earlier assumptions: what the build taught us"
---

Post 5 — What changed, what is still in progress, and an honest evaluation of the prototype against the brief

This post revisits the assumptions made in Posts 1 and 2 and asks which held up, which did not, and what the process of building revealed that planning could not have surfaced. The brief asks for evidence of evolving understanding ,this is where that evolution is most visible.

# What held up

The core framing from post 1,that visual artists need feedback pinned to the specific part of the image being discussed, not general commenting ,proved sound across every decision.when we got scope questions mid-build, asking "does this serve the core requirement?" gave a clear answer.The open-for-the-critique toggle passed the test. A heatmap overlay did not, heatmap is interesting but it does not change what critique feels like for the artist receiving it,so it stayed out of the scope.

 The percentage coordinate approach designed in Post 2 worked exactly as planned. Pins render accurately across screen sizes without server-side logic. The decision to store x and y as REAL rather than INTEGER,made to preserve decimal precision,proved correct when rendering pins at small scales where rounding would have introduced visible positioning errors.

# What Post 1 got wrong

The open-for-critique toggle was in the community analysis from the start, artists need control over when they receive feedback. It was not carried through to the requirements list. That gap between understanding the community and translating that understanding into concrete requirements is the clearest example of how the requirements list from Post 1 was incomplete, not wrong.

Accessibility was treated as a later phase when it should have been a Week 6 constraint. The annotation interaction was designed mouse-first. The popup edge problem,discovered during testing rather than designed out, is a direct consequence of building a spatial interaction without first asking how a keyboard user would navigate it. If the keyboard path had been prototyped first, the edge constraint would have been identified earlier and the design would have been stronger for everyone.

# Evaluating against the brief's standards

  - **Responsive design** — gallery and detail page work on mobile. Navigation collapses on small screens. Tested manually on device sizes

  - **Load time** — no formal Lighthouse test yet. Server-rendered HTML with HTMX partial updates has no client-side framework overhead, so performance is expected to be well within the 3-second limit. The risk is large image uploads, which file size limits will address post-submission

  - **Accessibility** — WCAG-required additions implemented. Formal WAVE and VoiceOver testing planned but not yet completed. The annotation canvas interaction remains the highest-risk area because spatial mouse interactions are what automated tools evaluate least reliably

  - **Cookie compliance** — banner is dismissable, stores user choice, and states clearly that no tracking or advertising cookies are used

### What is still to build

Annotation deletion and file size limits are the two outstanding requirements. Both are documented, both are scoped, and both will be built after this submission. They are not optional features — one is a legal obligation and one is a performance constraint the brief sets explicitly.

The most useful thing this process made clear is that a requirements list written in Week 6 is a hypothesis, not a specification. The open-for-critique toggle was always implied by the community analysis. The GDPR deletion requirement was always implied by storing user IDs in annotations. The accessibility gap was always implied by designing a spatial mouse interaction. Each of these was visible in the planning if the implications had been followed further. The brief's emphasis on thinking over narration reflects something real about how development works,the decisions that matter most are not the ones you make when building, but the ones you make when you understand what you are building for.