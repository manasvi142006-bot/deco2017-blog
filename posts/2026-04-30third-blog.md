---
title: THRID POST(Designing the pages)
date: 2026-04-30
author: MANASI SINGH
summary: "Designing the pages structure,layout, and building frontend first"
---
# Designing the pages: structure, layout,and building frontend first

Post 3 — Sitemap,key design decisions,and what building against dummy data revealed

This week we focused on building out all the pages,the gallery, upload form, detail page, and dashboard, before any real database logic was connected. Everything ran off dummy data. The reason for doing it this way was simple: if we built the backend first,we would end up designing pages around what was easy to query rather than what actually serves the community. Doing it this way forced every layout decision to be made on its own terms.

### Sitemap

![Sitemap](/deco2017-blog/assets/sitemap.png)
![User flow](/deco2017-blog/assets/userflow-diagram.png)
![Accessibility user flow](/deco2017-blog/assets/userflow-accessibility.png)


Each page links to a requirement directly. Nothing is there just for the sake of it. Our community’s space is the gallery. The main function is on the detail page. You can contribute via Upload. The dashboard allows artists to see how their work is resonating.

# The detail page — the hardest layout decision

This is the most important page so it got the most thoughts. Two columns,image on the left, annotation sidebar on the right. The image takes up most of the space because that is what the community is here for. The sidebar scrolls independently so you can read annotations without losing sight of the artwork.The sidebar has four filter pills,All, Composition, Colour, Anatomy, Lighting,each triggering an HTMX request that swaps the filtered list in without reloading the page.

There is also an "Open for Critique" toggle at the top of the page.When clicked it updates the artwork status instantly via HTMX.This gives artists control over when they receive feedback,something no general platform offers and something this community specifically needs.

When you click the image,a small popup appears right next to the click not a modal.A modal pulls focus away from the image and breaks the spatial connection between comment and location. The popup stays anchored to the click point,which is the whole point of the feature.It is also capped at 70% from the edges so it never gets cut off on smaller screens,something I only discovered was a problem when I actually started clicking around.After submitting, the orange pin appears on the canvas at the click position and the annotation appears in the sidebar immediately,no page reload.

The annotation list in the sidebar is not just decoration, it is the accessible version of the canvas.Every annotation is readable as text with its author,category,and comment.Keyboard users and screen reader users get everything through the list without ever touching the canvas.

# The upload page

The description field is optional for the artist but it becomes the alt text on the image, satisfying WCAG AA's image description requirement without making artists feel like they are filling in a form.The file input is a drag-and-drop zone with a live preview.Artists already use drag-and-drop every day in Figma and Procreate,using a plain file input would feel out of place.The submit button disables and says "Uploading..." on click to prevent accidental double submissions.

# Visual design

Dark UI,warm off-white text,terracotta pins.The dark theme is a practical choice for this community,artists spend long hours looking at work on screen and dark interfaces reduce eye strain.It also means the artwork itself stays the visual focus rather than competing with a bright white background.The login page has a paintbrush cursor effect built in vanilla JS using the Canvas API,no libraries, that paints brush strokes in the design system colours as you move your mouse.It sets the tone before anything else is read.

One thing I did not anticipate: the sidebar needed to scroll independently while the image stayed fixed.This only became obvious when I loaded in a lot of dummy annotations.Catching it before the backend was built made it a quick CSS fix. If I had found it after integration it would have been a much bigger problem to untangle.

![Detail page wireframe](/deco2017-blog/assets/wireframe-detail.png)
![My Artworks wireframe](/deco2017-blog/assets/wireframe-mine.png)
![Upload wireframe](/deco2017-blog/assets/wireframe-upload.png)