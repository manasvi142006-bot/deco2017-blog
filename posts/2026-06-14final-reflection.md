---
title: FINAL REFLECTION 
date: 2026-06-14
author: Manasvi Singh
summary: "A final reflection on Critique Canvas (evaluating performance, accessibility, and what the tools revealed that we didn't expect)."
---

 At the start of this project, I imagined Critique Canvas to be a dark, editorial space for artists to request accurate, structured feedback about their artwork. It included three main features: a spatial annotation canvas, a system for random collaboration, and a collaborative weekly story chain. Some features landed how I intended them to, Others exposed gaps I didn't see coming.

EVALUATION METHODOLOGY

To assess performance, I implemented Chrome DevTools Lighthouse for both desktop and mobile versions of the site. Implemented WAVE to evaluate accessibility on four pages and assessed the contrast of site colors with WebAIM's contrast color checker. For usability, I performed one peer walkthrough. For all tests, I accessed the site via localhost in an incognito mode.

PERFORMANCE 

67 on desktop, 74 on mobile, not where I wanted to land. But when I actually looked at what Lighthouse was flagging, the causes made sense.
The main issue is Largest Contentful Paint: 14.1 seconds on desktop and 13.1 on mobile. Both come back to two decisions we made early and never revisited. Artwork images are served as raw uploaded files, no compression were made, no resizing, no format conversion, so the entire file has to download before the browser can paint the largest element on screen. On top of that, Sora and Space Mono are loaded via a CSS @import at the top of the global stylesheet, which creates a render-blocking request costing an estimated 2,950ms. The @import does include display=swap so text falls back to a system font while Sora loads, but the blocking request itself is still the bottleneck. Both decisions had reasons behind them. Neither accounted for load time. That's where our planning fell short.
Best Practices came in at 96 rather than 100. Nothing in testing pointed to a functional cause, my best read is that it reflects environment-specific behaviour during the localhost audit.
Two metrics came back genuinely strong though. Total Blocking Time was 0ms on both desktop and mobile, a direct result of using HTMX rather than a heavier JavaScript framework that would have blocked the main thread. Cumulative Layout Shift was 0.003 on desktop and 0.001 on mobile, essentially nothing, because every artwork card has a fixed aspect-ratio: 4/3 on the .card-image element so the layout never jumps around as images load in. Small decision in the stylesheet, clean result in the audit.

## Lighthouse Audit Results — Desktop vs Mobile

| Metric | Desktop `/artwork` | Mobile `/artwork/mine` |
|---|---|---|
| Performance | 67 | 74 |
| Accessibility | 96 | 96 |
| Best Practices | 96 | 96 |
| SEO | 100 | 100 |

## Core Web Vitals

| Metric | Desktop | Mobile | Status |
|---|---|---|---|
| First Contentful Paint | 3.6s | 1.9s | 🟡 |
| Largest Contentful Paint | 14.1s | 13.1s | 🔴 Poor |
| Total Blocking Time | 0ms | 0ms | 🟢 Good |
| Cumulative Layout Shift | 0.003 | 0.001 | 🟢 Good |
| Speed Index | 3.6s | 1.9s | 🟡 |

## Lighthouse Diagnostics — Desktop

| Issue | Estimated Savings |
|---|---|
| Render-blocking requests | 2,950ms |
| Improve image delivery | 2,118 KiB |
| Efficient cache lifetimes | 2,349 KiB |
| Reduce unused CSS | 40 KiB |
| Reduce unused JavaScript | 42 KiB |
| Minify CSS | 17 KiB |

![Lighthouse desktop scores](/deco2017-blog/assets/lighthouse-desktop.png)
*Lighthouse desktop audit — /artwork page. Performance 67, Accessibility 96, Best Practices 96, SEO 100*

![Core Web Vitals](/deco2017-blog/assets/core-web-vitals.png)
*Core Web Vitals — LCP 14.1s flagged red. TBT 0ms and CLS 0.003 green*

![lighthouse mobile](/deco2017-blog/assets/lighthouse-mobile.png)
Lighthouse mobile audit — /artwork/mine. Performance 74, LCP 13.1s, TBT 0ms, CLS 0.001*



USER EXPERINCE AND ACCESSIBILITY 

Zero errors across every page tested in WAVE is the result I am most satisfied with. ARIA labels are in place, form inputs have associated labels, skip links exist on every page, and the navigation landmark structure is correct throughout.  we wrote Semantic HTML and ARIA were built in from the start rather than retrofitting it.
Contrast failures tell a more complicated story. Running actual colour values through WebAIM's contrast checker revealed the failures are not on muted metadata text as I initially assumed, text-muted (#a4a4a8) returns 6.92:1 and 7.59:1 against the surface and base backgrounds, both passing. The actual failures are on the brand's primary orange: white text on (#ff6b35) returns just 2.83:1, well below the 4.5:1 WCAG AA threshold, failing for normal text, large text, and UI components. This affects every primary action, the Browse Gallery and Upload Artwork buttons, the active navigation link, the cookie consent button, and the Claim Panel button in Story Chain. The colour was picked for its energy and warmth, it was never checked against white text. That's a decision that should have happened at token definition time, not evaluation time.

WAVE also flagged three instances of very small text on the homepage, the monospace feature highlight labels inside the feature cards. A deliberate typographic choice, but small decorative text creates a real barrier for low-vision users regardless of intent. An aesthetic decision and an accessibility requirement pulled in opposite directions without us explicitly acknowledging that trade-off.

Keyboard navigation testing showed strong results across most of the application, navigation links, buttons, forms, the interests selection pills on Collab Roulette, and the Story Chain panel claiming all tab through correctly with the orange :focus-visible outline. The one gap is the annotation canvas. There is no keyboard path to placing a pin, the interaction is entirely mouse-dependent. Every other part of the application is keyboard accessible, main feature can only be achieved through the use of a mouse. That asymmetry is a meaningful gap our original requirements never addressed.

Peer usability testing surfaced something neither tool could catch. When we asked them to leave an annotation, the first instinct was to look for a button. There is no affordance label, nothing that says "click anywhere to annotate." The tester spent about fifteen seconds before trying a click. But once discovered, everything worked as intended. Discoverability is the failure, not the feature. Collab Roulette page has the same structural problem: the interests panel and spin wheel sit on the same page with equal visual weight, requiring scroll to find the primary action with no signal about which step comes first.


![Lighthouse accesibility scores](/deco2017-blog/assets/lighthouse-accessibility.png)
*Lighthouse accessibility — 96/100. Contrast ratio failure flagged. 10 items require manual review*


## WAVE Accessibility Evaluation

| Page | Errors | Contrast Errors | Alerts | AIM Score |
|---|---|---|---|---|
| Homepage (`/`) | 0 | 5 | 5 | 7.5/10 |
| My Artworks (`/artwork/mine`) | 0 | 4 | 1 | 7.3/10 |
| Collab Roulette (`/collab`) | 0 | 4 | 1 | 7.9/10 |

![wave-artworks](/deco2017-blog/assets/wave-artworks.png)
*WAVE — My Artworks. 0 errors, 4 contrast errors, AIM 7.3/10*

![wave-roullete](/deco2017-blog/assets/wave-roullete.png)
*WAVE — Collab Roulette. 0 errors, 4 contrast errors, AIM 7.9/10*

## WebAIM Contrast Checker Results

| Pairing | Ratio | WCAG AA | Where Used |
|---|---|---|---|
| `#ffffff` on `#ff6b35` | 2.83:1 | ❌ Fail | All primary buttons, active nav, cookie banner |
| `#a4a4a8` on `#161b27` | 6.92:1 | ✅ Pass | Card metadata, sidebar |
| `#a4a4a8` on `#0f1117` | 7.59:1 | ✅ Pass | Footer, page-level text |


![contrast muted pass](/deco2017-blog/assets/contrast-muted-pass.png)
*WebAIM — #a4a4a8 on #161b27. Ratio 6.92:1 — passes WCAG AA. Muted text was not the source of failures*

![contrast orange fail](/deco2017-blog/assets/contrast-orange-fail.png)
*WebAIM — #ffffff on #ff6b35. Ratio 2.83:1 — fails WCAG AA for normal text, large text, and UI components*



CRITICAL REFLECTION AND IMPROVEMENT

Image optimisation is where i'd start if development continued. Server-side processing on upload, resize to maximum display width, convert to WebP, generate separate thumbnails for gallery cards addresses the root cause and realistically brings LCP under 4 seconds. The render-blocking font problem is fixed by replacing the CSS @import with <link rel="preload"> in the HTML head. The display=swap is already in the URL, so this one change removes the blocking behaviour without any other modification.
The orange contrast failure needs a design decision. Darkening #ff6b35 enough to pass 4.5:1 against white would alter the brand character significantly. The more practical fix is switching primary button text from white to near-black, a one-line change in .btn-primary that resolves the failure across every affected element simultaneously.
The annotation discoverability problem needs a persistent canvas label, something like "Click anywhere to leave a critique" as a small overlay fading after the first pin. The Collab Roulette fix is structural: collapse the interests panel by default once interests are saved, making the wheel the primary visual on load. Both share the same root cause, we built for users who already understood the features rather than users encountering them for the first time. If I had one sprint, image optimisation comes first, the single change with the largest measurable impact on the metric most likely to cause users to leave before the page is usable.

RETROSPECTIVE ASSESSMENT OF FUNCTIONAL REQUIREMENTS

## Original Functional Requirements vs. Final Delivery

Almost all original requirements were well-scoped, and we fulfilled them. Sequential panel lock Story Chain ensures server-side works just like specified, panels 2 through 8 remain locked until the preceding submission is complete.
The most instructive gap is the one never written as a requirement: image optimisation. We specified that users could upload artwork and view it in the gallery but never specified how those images should be served. That omission is exactly why LCP sits at 14.1 seconds on desktop. Feature requirements without quality requirements are incomplete, I did not understand that going in and do now.
However, one cannot just look past the lack of full completion of the WCAG AA requirement. We wrote it as a single line without defining what compliance meant, no specific criteria, no testing method. The accessibility work was genuine, zero WAVE errors and 96/100 Lighthouse reflect real effort. But the brand orange failing at 2.83:1 on every primary button means we did not clear the bar we set. A better version would have specified contrast ratios at colour token definition time.
The Collab Roulette requirements were the most ambiguous. We built smart matching, 24-hour windows, real-time chat, and file submission, everything specified. But we never defined what "collaboration" meant in practice. The result is two users working in parallel on a shared prompt rather than genuinely co-creating. That ambiguity was in the requirement from the start.

| Requirement | Status | Notes |
|---|---|---|
| Upload artwork with title, description, medium | ✅ Delivered | Fully functional |
| Spatial annotation pins with x/y coordinates | ✅ Delivered | Percentage-based, resolution-independent |
| Category filter pills via HTMX | ✅ Delivered | Instant partial swap, no reload |
| Open/closed for critique toggle | ✅ Delivered | HTMX outerHTML swap |
| Upvote and helpful buttons on annotations | ✅ Delivered | Toggle behaviour, aria-pressed |
| Collab Roulette — spin and smart match | ✅ Delivered | Interest-based matching with fallback |
| Collab Roulette — 24hr challenge with chat | ✅ Delivered | Message expiry enforced server-side |
| Story Chain — 8-panel sequential submission | ✅ Delivered | Sequential lock, HTMX polling |
| Mobile responsive navigation | ✅ Delivered | Hamburger menu with ARIA |
| WCAG AA accessibility compliance | ⚠️ Partial | Brand orange fails contrast at 2.83:1 |
| Image optimisation on upload | ❌ Not scoped | Never written as a requirement |


LESSONS LEARNED 

The most lasting thing this project clarified is the difference between a feature that works and a feature that is ready. Every feature in Critique Canvas works. But ready means a first-time user discovers the interaction without confusion, images load in under four seconds, and core actions are reachable without a mouse. These are quality failures that only surface when you evaluate with real users and tools rather than with yourself.
The contrast finding was the most surprising. I assumed the failures would be in the subtle, muted parts of the interface. The actual values showed the opposite: muted text passes comfortably, and the brand orange on every primary button is the one that fails. The aesthetic decision that defines the visual identity is also its most significant accessibility problem. That is not something you find by looking at the design. You only find it by measuring it.
The image optimisation gap was not negligence, it was a category error. Functional requirements describe what a system does. Quality requirements describe how well it does it. A requirement that says "users can upload and view artwork" is incomplete without one that constrains how those images are delivered. That distinction will change how I write requirements on every project after this one.