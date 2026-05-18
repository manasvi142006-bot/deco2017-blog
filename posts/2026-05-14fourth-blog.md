---
title:  FOURTH POST(Connecting the backend)
date: 2026-05-14
author: Manasvi singh 
summary:  Connecting the backend, refining scope, and planning for evaluation
---


Post 4 — Scope decision in the real world, what changed in the build, and how we are going to test the prototype

As we wire up the backend and swap out dummy data with actual database queries,earlier assumptions are put to the test. In this post I talk about one big scope change that only became apparent when real data was collected for the system and describe the evaluation plan for the prototype that has not yet been fully tested.

# A Scope Decision That Became Clear Only When Data Was Available

Post 1 mentioned the open-for-critique toggle as a secondary feature. Once actual artworks were present in the gallery, it was clear that this was one of the core requirements. Without such,anyone can annotate any art work at any time.including work that the artist considers unfinished or amended beyond the uploaded version.A gallery that lacks this control places the reviewer’s convenience over the artist,which is the contradicts of what this community hub is for.

The toggle is implemented as a SQL CASE statement flipping the open_for_critique integer between 1 and 0,a MojoJS POST route, and an HTMX partial swap updating the button state instantly without a page reload. Canvas click events are blocked in JavaScript when the value is 0. The cost was one column, one route, one partial. It should have been in the core requirements from Post 1,the community analysis supported it,the implication just was not followed through.

# What is still remaining 

Two requirements from earlier posts have not been met yet. Annotation deletion ,identified as a GDPR obligation in Post 2  has a data model and a planned route but no completed UI. File size limits on upload ,flagged as a performance risk in Post 2 are not yet enforced. Both are scoped and documented. Neither is optional in a production context, deletion is a legal requirement,and file limits are what keeps the application within the brief's 3-second load time requirement under realistic conditions.

# Accessibility additions made this phase

The brief requires AA compliance under WCAG 2.1. The following were added based on WCAG requirements rather than testing results, since formal testing has not yet been completed:

  - Skip link appearing on Tab press — required under WCAG 2.4.1 for keyboard navigation

  - Visible orange focus outline on all interactive elements — required under WCAG 2.4.7

  - ARIA labels on all four filter pills — Composition, Colour, Anatomy, Lighting

  - Cookie banner with aria-live so screen readers announce it on load

  - Annotation pins with aria-label containing comment, author, and category

Formal accessibility and performance evaluation has not yet been completed. The plan is to run WAVE automated testing first, then manually test with VoiceOver on macOS,automated tools catch roughly 30-40% of issues, and the annotation canvas interaction is exactly the kind of spatial, mouse-dependent feature that automated tools evaluate poorly. Lighthouse will be used to measure load time under simulated 4G conditions. The annotation canvas and large image uploads are the two highest-risk areas for both accessibility and performance, and those will be the focus of testing.