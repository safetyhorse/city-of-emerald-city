# Clearfix: catching up on the CSS you've missed

Demo materials for a conference session on modern CSS, prepared for **NAGW**
(National Association of Government Web Professionals). The session reviews CSS features
that are safe to use *now* (Fall 2026) and retires a decade of workarounds, aimed at
developers whose CSS skills predate the recent wave of native features.

The centerpiece is a single fictional municipal site — the **City of Emerald City**
(from L. Frank Baum's public-domain *Wonderful Wizard of Oz*, 1900) — shown twice: once as
a 2011-era mess, once rebuilt with modern CSS. Around it are standalone demos and printable
reference sheets.

---

## What's in here

**The site**

| File | What it is |
|------|------------|
| `emerald-city-before.html` | A working 2011-era municipal site built entirely from dated hacks (floats, clearfix, hardcoded colors, `!important`, vendor prefixes, jQuery). Every hack is a refactoring target. |
| `emerald-city-after.html` | The same site rebuilt with modern CSS — cascade layers, custom properties + `color-mix()`, nesting, container queries, `:has()`, logical properties, `light-dark()`. **Contains no JavaScript.** |

**Reference & handouts**

| File | What it is |
|------|------------|
| `before-and-after.md` | Feature-by-feature explainer of the two files: the old technique, the native feature that replaces it, and its Baseline status. Ends with a short appendix for anyone running it as a live session. |
| `delete-list-cheat-sheet.html` | One-page printable handout: old hack → native replacement → Baseline status → MDN link. |
| `keep-in-the-quiver.html` | Companion handout: fundamentals that are still reliable, tools that still have a narrow job, and modern additions worth knowing. |
| `emerald-city-layout-patterns.html` | Drag-to-resize demos: a responsive table (two ways), a list arranged with flexbox / grid / multi-column, and a page skeleton (grid for regions, flexbox inside, sticky header). |

**Interactive demos (pens)**

Each is a small, self-contained page focused on one idea — made to open and poke at, or to
live-edit on stage.

| File | What it shows |
|------|------------|
| `pen-container-query.html` | The same card in a wide and a narrow container at once, with an editable `@container` threshold. |
| `pen-logical-properties.html` | Physical vs logical properties across left-to-right, right-to-left, and vertical writing modes. |
| `pen-has.html` | Four `:has()` patterns: style a parent by its contents, adapt layout to content, react at a distance, and replace jQuery form validation. |
| `pen-layers-important.html` | Cascade layers beating specificity, and `!important` escaping the layers (delete it live in DevTools to see the fix). |
| `pen-typography.html` | Fluid type with `clamp()`, and `text-wrap: balance` / `pretty`. |
| `pen-flow-root-study.html` | Float containment and margin collapse, compared across nothing / clearfix / `display: flow-root`. |
| `pen-ui-patterns.html` | Native accordion (`<details>`), modal (`<dialog>`), and popover — and why CSS-only tabs aren't accessible. |
| `pen-tailwind.html` | The same button and card in vanilla CSS and in Tailwind utilities, with notes on what Tailwind is and who it helps. |

**Dependency**

| File | What it is |
|------|------------|
| `lib/jquery-1.7.1.min.js` | Required by the `before` file, and only that file. |

---

## Running the demos

These are plain static HTML files — no build step, no dependencies to install.

- **Simplest:** double-click any `.html` file to open it in a browser.
- **Optional local server** (for a clean `http://` context):
  ```
  python3 -m http.server
  ```
  then visit `http://localhost:8000`. VS Code's "Live Server" extension works too.

### The one required file: jQuery

Only the `before` file uses JavaScript, and it loads jQuery **locally** (period-accurate
1.7.1) so it runs offline — no CDN to fail on conference wifi. It's committed at
`lib/jquery-1.7.1.min.js`; make sure the `before` file's `<script src>` points to that
path. Setting the repo up fresh? Grab the compressed 1.7.1 build from
<https://code.jquery.com/jquery-1.7.1.min.js> and drop it there.

The `after` file and every pen have no dependencies at all.

---

## The before → after concept

The session works through the `before` file top to bottom, refactoring each hack and adding
it to the Delete List. The `after` file is the reveal at the end. The single most useful
contrast: the `before` file needs jQuery and a pile of media queries to do what the `after`
file does with container queries, `:has()`, and native form validation — and the `after`
file ships zero JavaScript.

See `before-and-after.md` for the full feature-by-feature explainer; its appendix has a
suggested live-demo order.

---

## Publishing (optional)

Everything is static, so the repo can be served as-is via **GitHub Pages** with no
configuration — useful if you want attendees to open the demos or the cheat sheets after
the session. Enable Pages in the repo settings and the files are live.

---

## A note on Baseline status

The cheat sheets and `before-and-after.md` label each feature **WIDELY** (safe to ship) or
**NEWLY** (works in current browsers; wrap in `@supports` or accept graceful degradation for
older ones). These statuses drift over time — several features here only crossed their
thresholds in 2026, and `light-dark()` is due to reach Widely around November 2026. Every
row links to MDN, which shows the live Baseline badge, so a status check takes minutes.
Re-verify the week before presenting.

To enforce a floor in a real codebase, the ESLint rule
`css/require-baseline: ["warn", { available: "widely" }]` flags any feature that hasn't
reached Widely available yet.

---

## Credits & licensing notes

- "Emerald City," the "Land of Oz," and the "Yellow Brick Road" come from the 1900 novel,
  which is in the public domain. Content deliberately avoids elements original to the 1939
  film (which remain under copyright).
- All names, addresses, and contact details in the demos are fictional.