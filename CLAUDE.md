# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Stack & Conventions

This project has two hard constraints that must never be violated:

1. **Single-file project.** The entire project must live in one `index.html`
   file, with all CSS and JavaScript inlined directly in that file using
   `<style>` and `<script>` tags — never in separate `.css` or `.js` files,
   and never split across additional `.html` pages. Linking to external
   images, and external CSS/JavaScript libraries via `<link>`/`<script src>`
   (e.g. a CDN), is allowed. This constraint exists so the finished project
   can be copy-pasted as a single file for sharing in class and on
   single-file code platforms (e.g. CodePen, JSFiddle).
2. **Vanilla only, no build step.** Use plain HTML, CSS, and JavaScript
   only — no frameworks or libraries that require a build/bundle step
   (e.g. React, Vue, TypeScript, Sass, Webpack/Vite). The file must run by
   simply opening it in a browser, with no compilation or tooling required.

## Tech stack (hard constraints — do not deviate)
- Vanilla HTML, CSS, and JavaScript only. No React, Vue, or any JS framework.
- Tailwind CSS for all styling (via CDN only).
- No backend, no database. Fully static site.
- A toggle for light and dark theme, with the choice remembered
  across visits.

## Working conventions
- Before implementing any non-trivial feature, ask clarifying
  questions about scope, edge cases, and constraints first —
  don't propose a plan until you've asked.

## Feature Plan

Living plan for the portal, broken into phases. Update this section as
phases complete: mark a phase done, or prune its detail down to a one-line
summary once it's stable, so this stays skimmable as the collection grows.

### Data model
- `TOOLS`: JS array of `{ id, title, description }`, defined once in
  `index.html`. Drives both the home card grid and which `<section>` a
  card opens. Adding a tool later = one array entry + one `<section>` +
  its own render/compute functions — no shell changes.
- Each tool owns its `<section id="view-{id}">` and an IIFE module in the
  main `<script>` block scoping its own state/DOM/canvas logic.
- Theme state: single string (`"light"` / `"dark"`) in `localStorage` under
  `tools-portal-theme`, applied as the `dark` class on `<html>`.

### Key flows
- **Navigation**: click a home card → `showView(id)` hides all
  `main > section` and unhides the target. Back button / site title →
  `showView('home')`. No routing, no reload.
- **Theme**: on load, read `localStorage` (fallback to
  `prefers-color-scheme`) and apply. Toggle button flips the class,
  re-persists, and each tool module redraws its canvas so colors stay
  correct.
- **Aerofoil flow**: NACA 4-digit input → validate (`/^\d{4}$/`) → parse to
  camber/position/thickness → generate coordinates (cosine-spaced) → draw
  outline on canvas. Invalid input shows an inline message instead of
  drawing.
- **FBD flow**: force rows (Fx/Fy) → sum on any input change → draw each
  force arrow + the resultant (distinct style) on canvas, scaled to fit →
  update the ΣFx/ΣFy/magnitude/angle readout. Non-numeric input treated as
  0; add/remove rows re-renders and recomputes.

### Phases

**Phase 1 — Portal shell + first two tools**
- Home card grid, single-page view switcher, persisted light/dark toggle.
- Tool: Aerofoil (NACA 4-digit) visualizer — outline plot only.
- Tool: Free Body Diagram calculator — Cartesian force input, resultant
  vector + readout.
- Proves the pattern: adding tool N+1 shouldn't require shell rework.

**Phase 2 — Third+ tool(s), TBD**
- Scope not yet defined. When starting, follow Working conventions above:
  ask clarifying questions before proposing a plan for the specific
  tool(s).
- Should validate the pattern holds with 3+ tools (e.g. does the home grid
  need categories/search yet, or is a flat grid still fine).
