# Clearfix: catching up on the CSS you've missed

Demo materials for a conference session on modern CSS, prepared for **NAGW**
(national association of government web professionals). The session reviews CSS features that are safe to
use *now* (Fall 2026) and retires a decade of workarounds, aimed at developers whose CSS skills
predate the recent wave of native features.

The centerpiece is a single fictional municipal site — the **City of Emerald City**
(from L. Frank Baum's public-domain *Wonderful Wizard of Oz*, 1900) — shown twice:
once as a 2011-era mess, once rebuilt with modern CSS.

---

## What's in here

| File | What it is |
|------|------------|
| `emerald-city-before.html` | The "before": a working 2011-era municipal site built entirely from dated hacks (floats, clearfix, hardcoded colors, `!important`, vendor prefixes, jQuery). Every hack is a live refactoring target. |
| `emerald-city-after.html` | The "after": the same site rebuilt with modern CSS — cascade layers, custom properties + `color-mix()`, nesting, container queries, `:has()`, logical properties, `light-dark()`. **Contains no JavaScript.** |
| `delete-list-cheat-sheet.html` | One-page printable handout: old hack → native replacement → Baseline status → MDN link. |
| `keep-in-the-quiver.html` | Companion handout: fundamentals that are still reliable, tools that still have a narrow job, and modern additions worth knowing. |
| `demo-walkthrough.md` | Presenter script: for each feature, where to point in the `before` file, what to swap it to, the line to say, and the status/gotcha. |
| `lib/jquery-1.7.1.min.js` | Required by the `before` file. |

---

## Running the demos

These are plain static HTML files — no build step, no dependencies to install.

- **Simplest:** double-click any `.html` file to open it in a browser.
- **Optional local server** (nice for a clean `http://` context):

### The one required file: jQuery

The `before` file depends on jQuery, saved **locally** for its age and so it runs offline.

The `after` file has no dependencies at all.

---

## The before → after concept

The session works through the `before` file top to bottom, refactoring each hack live
and adding it to the Delete List. The `after` file is the reveal at the end. The single
most useful contrast: the `before` file needs jQuery and a pile of media queries to do
what the `after` file does with container queries, `:has()`, and native form validation —
and the `after` file ships zero JavaScript.

See `demo-walkthrough.md` for the full feature-by-feature script and a suggested
live-demo order.

---

## A note on Baseline status

The cheat sheets label each feature **WIDELY** (safe to ship) or **NEWLY** (works in
current browsers; wrap in `@supports` or accept graceful degradation for older ones).
These statuses drift over time — a few features here only crossed their thresholds in
2026. Every row links to MDN, which shows the live Baseline badge, so a status check
takes minutes. Re-verify the week before presenting.

To enforce a floor in a real codebase, the ESLint rule
`css/require-baseline: ["warn", { available: "widely" }]` flags any feature that hasn't
reached Widely available yet.

---

## Credits & licensing notes

- "Emerald City," the "Land of Oz," and the "Yellow Brick Road" come from the 1900
  novel, which is in the public domain. Content deliberately avoids elements original to
  the 1939 film (which remain under copyright).
- All names, addresses, and contact details in the demo are fictional.