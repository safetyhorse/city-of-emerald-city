# Before and after: a modern-CSS rebuild, feature by feature

This repo contains the same fictional municipal site twice. `emerald-city-before.html` is a
working 2011-era stylesheet, built from the hacks of its day. `emerald-city-after.html` is
the same site rebuilt with modern CSS. This document walks the comparison one feature at a
time: the old technique, the native feature that replaces it, and whether it's safe to use
today.

The headline difference is that the after file contains no JavaScript. Every behavior the
before file needed jQuery for is now native CSS or HTML.

Status key: **WIDELY** = safe to ship. **NEWLY** = works in current browsers; wrap in
`@supports` or accept graceful degradation for older ones. Statuses are current as of
October 2026; check MDN for the latest.

---

## 1. The reset & vendor prefixes

The before file opens with `* { margin:0; padding:0 }` and vendor-prefixed stacks
(`-webkit-`, `-moz-`, `-ms-`) on `border-radius`, `box-shadow`, and `box-sizing` — half of a
2011 stylesheet was typing the same property four times. The after file uses a one-line
`box-sizing` reset and unprefixed properties throughout. The prefixes have been unnecessary
for years. **(WIDELY.)**

## 2. Floats + clearfix → Grid/Flexbox + gap

The before file lays out the page with floats: `#content` and `#sidebar` float opposite
directions, `.clearfix` appears throughout to contain them, and the card row uses
negative-margin gutters (`.services { margin: 0 -10px }`). Clearfix existed only to clean up
after floats. The after file uses `display: grid` with `gap` for the page columns and an
intrinsic `repeat(auto-fill, minmax(11rem, 1fr))` track for the cards; deleting the floats
removes the clearfix with them, and `gap` replaces the negative-margin math. Note that `gap`
works in flexbox now, not only grid. **(WIDELY.)**

## 3. A JS "container query" + media-query ladder → `@container`

The before file approximates a container query with JavaScript: `checkCardWidth()` measures
each card and toggles an `.is-narrow` class, while a separate set of `max-width` breakpoints
re-flows the card grid. The two systems disagree — resize slowly and the cards change at
widths that don't line up. The after file makes each card a container
(`container-type: inline-size`) and adapts its internals with a single
`@container (max-width: 12rem)` rule; the grid track handles the 3→2→1 reflow on its own,
with no breakpoints.

The distinction: the old code asked the *window* how wide it was, but a card cares about the
column it sits in, not the window. Container queries let a component respond to its own size,
so the same card renders differently in the wide column and the narrow sidebar. This removes
the JavaScript and the media queries together. **(WIDELY, for size queries.)**

## 4. A min-width heading ladder → `clamp()`

The before file resizes the hero heading with three `@media (min-width: …)` overrides. The
after file uses one rule — `font-size: clamp(1.4rem, 1rem + 3vw, 2.5rem)` — fluid at every
width between its floor and ceiling instead of jumping at magic numbers. The before file also
mixes `min-width` for the heading and `max-width` for the layout; that inconsistency is part
of why hand-grown responsive CSS rots, and `clamp()` sidesteps the breakpoint-direction
question entirely. **(WIDELY.)**

## 5. Awkward heading wrap → `text-wrap`

The before file's long hero headline wraps with a single word stranded on the last line — the
kind of thing people used to fix with a JavaScript balancer or a hand-placed `<br>` that had
to be re-tweaked whenever the copy changed. The after file uses `text-wrap: balance` on
headings and `text-wrap: pretty` on paragraphs; the browser evens the lines itself. `balance`
is **WIDELY**; `pretty` still lags in Firefox, so treat it as a progressive enhancement. Both
degrade to normal wrapping, so neither needs `@supports`. `balance` is the clear improvement;
`pretty` is subtle.

## 6. The padding-bottom ratio hack → `aspect-ratio`

The before file holds the map embed's proportions with the padding hack
(`padding-bottom: 56.25%; height: 0`) and an absolutely-positioned child — 56.25% being 9
divided by 16. The after file uses `aspect-ratio: 16 / 9`. **(WIDELY.)**

## 7. Anchor jumps under the sticky bar → `scroll-margin-top`

In the before file, clicking an in-page jump link lands the target heading hidden behind the
sticky bar — a real, common bug. The after file sets `scroll-margin-top` on the targets (or
`scroll-padding-top` on the scroll container) and uses a plain `position: sticky` nav, which
also deletes the scroll-position JavaScript. The old fix was an invisible pseudo-element with
a negative margin; the real fix is one property telling the browser where to stop.
**(WIDELY.)**

## 8. `:has()` — the parent selector

The before file's form validation walks *up* the DOM in jQuery — `.closest('.field')` — to
tag a field's parent, because for twenty years CSS could only look downward. The after file
writes `.field:has(:user-invalid)`. Styling an element based on what it contains was the
entire reason that script existed. **(WIDELY — reached Baseline Newly in late 2023, Widely in
2026.)**

## 9. Pure-CSS form validation → `:user-invalid`

The rest of the before file's validation is a submit handler, manual empty-checks, and class
toggling. The after file adds `required` attributes and styles with `:user-invalid`, combined
with `:has()` to light up the field. `:user-invalid` fires only after the user has
interacted, which also fixes the old problem where `:invalid` flagged empty fields on page
load. This removes the last of the form JavaScript. **(WIDELY, May 2026.)**

## 10. `:focus-visible` — the accessibility fix

The before file contains `a:focus { outline: none }` — an accessibility regression that
shipped on many sites to remove the "ugly" focus ring, which also removed it for keyboard
users who need it. The after file uses `:focus-visible`: the ring shows for keyboard focus and
not for mouse clicks, giving each input method the right behavior. This is a Section 508 /
WCAG fix, not only an aesthetic one. **(WIDELY.)**

## 11. `accent-color` — themed controls without the hack

The before file fakes a themed checkbox by hiding the real input off-screen
(`left: -9999px`) and swapping a background image on a `.box` element, discarding the native
control's keyboard and screen-reader behavior. The after file uses a real
`<input type="checkbox">` with `accent-color: var(--brand)`. One property themes the native
control and keeps its built-in accessibility. **(WIDELY.)**

## 12. `field-sizing` — the auto-grow textarea

The before file grows the textarea with a jQuery `input` handler that measures `scrollHeight`.
The after file uses `field-sizing: content` — the textarea grows with its content, no
measuring, no JavaScript. This is **NEWLY** (all engines as of June 2026), and it's the
clearest example in the set of adopting a new feature safely: wrap it in
`@supports (field-sizing: content) { … }`, or simply accept that older browsers get a fixed
textarea, which is a fine fallback.

## 13. `scrollbar-color` / `scrollbar-width`

The before file styles the footer index scrollbar with `::-webkit-scrollbar` rules, which
Firefox ignored entirely — so a third of users saw a default scrollbar. The after file uses
the standard `scrollbar-width` and `scrollbar-color`, which work across browsers. **NEWLY**
(`scrollbar-color` completed the set in December 2025 when Safari shipped it); degrades to the
default scrollbar, so no `@supports` needed.

## 14. jQuery slide/fade → `<details>`

The before file's read-more toggle is jQuery `.slideToggle()`. The after file uses native
`<details>`/`<summary>` — expand-and-collapse is a browser primitive. If you want it animated,
`@starting-style` and animating to `display: none` remove the last reason for a JS animation
library. `<details>` is **WIDELY**; the animation pieces are **NEWLY** (heading to Widely
around early 2027).

---

## Two faces of `!important`

The before file includes two `!important` declarations on purpose, because they teach
opposite lessons.

The first is reflexive insurance:

```css
.services .card.featured .inner { border-color: #f0c419 !important; background: #fffdf3 !important; }
```

Remove both `!important`s and nothing changes: the `.featured` selector already outranks the
base `.card .inner` on specificity, so the `!important` was never winning anything. This is
how `!important` usually appears in the wild — not resolving a conflict, just added "to be
safe." Nobody checks, nobody removes it, and it accumulates until every override needs its own
`!important` to beat the last one.

The second is legitimate — overriding a strong selector you can't, or daren't, change:

```css
#wrapper #hero .searchbox input[type="submit"] { background: #999999; }   /* can't edit / risky to touch */
#hero .searchbox input[type="submit"] { background: #046a38 !important; } /* our only lever */
```

Remove this `!important` and the button reverts from emerald to gray, because the first
selector (two IDs) outranks the second (one ID). Here `!important` is the correct tool: the
stronger rule is one you can't change — a vendor CMS, or legacy code where the consequences of
deleting it are unknown. Your stylesheet is weaker, so `!important` is the honest way to win.

Cascade layers don't always rescue the second case. Layers help only if you can place your
styles in a layer that outranks the other rule — but unlayered styles beat all layered normal
styles, and vendor or legacy stylesheets are almost always unlayered. When you can only append
your own CSS against an unlayered, high-specificity rule you can't touch, `!important` (or
out-specificing it) is the answer.

A test for your own code: delete the `!important`. If nothing changes, it was the reflexive
kind — leave it gone. If something breaks and you can't fix the *other* rule, it's the
legitimate kind — keep it, and leave a comment explaining why.

---

## Architecture: the quieter rebuild

Several changes don't map to a single hack but run through the whole after file, and together
they're what make it maintainable.

**Native nesting** groups each component's rules in one block — the main reason people kept a
preprocessor. **(WIDELY.)**

**Custom properties + `color-mix()`** replace the hardcoded palette. The before file repeats
the emerald green dozens of times with hand-computed tints; the after file defines one
`--brand` token and derives every shade with `color-mix()`. Change the brand in one place.
This also replaces Sass color functions, natively. **(WIDELY.)**

**Cascade layers + `:where()`** replace the specificity fights. The before file has an
over-qualified `body #main #content .services .card .inner .card-title` selector and the two
`!important` band-aids above; the after file declares its `@layer` order once, so nothing
needs to fight. Layers organize normal declarations; `!important` still escapes them, which is
why the goal is to remove the need for it rather than to out-layer it. **(WIDELY.)**

**Logical properties** (`margin-inline`, `padding-block`, `text-align: start`,
`inset-block-start`) make the layout direction-independent — the same code works for
right-to-left and vertical writing systems. **(WIDELY.)**

**`light-dark()` + `color-scheme`** give the after file dark mode from one set of tokens:
toggle your OS theme and reload, and it flips with no second stylesheet. **(NEWLY**, crossing
to Widely around November 2026; `prefers-color-scheme` is the widely-available fallback.)

---

## Appendix: running this as a live session

If you want to present the before→after as a live refactor rather than read it, a few notes.

Suggested order (builds momentum and groups related changes):

1. Vendor prefixes + reset — an easy opener that sets the pattern.
2. Floats → Grid + gap — a large visual change.
3. `@container` — removes JavaScript and breakpoints together.
4. `clamp()` + `text-wrap` — quick typography wins.
5. `aspect-ratio` + `scroll-margin-top` — two fast hack-kills.
6. `:has()` + `:user-invalid` + `accent-color` + `field-sizing` — the
   forms-lose-their-JavaScript run; end on the empty script tag.
7. `:focus-visible` — the accessibility point.
8. Architecture recap: nesting, tokens, layers, logical properties, dark mode.
9. Reveal the after file top to bottom, ending on its closing comment: no `<script>`, no
   jQuery.

The two `!important` cases are a natural place to bring in a second presenter with a CMS or
legacy-maintenance perspective — one case is the reflexive habit to drop, the other is the
constrained reality where `!important` is still correct.

Flag the NEWLY features — `field-sizing`, `scrollbar-color`, `light-dark()`,
`text-wrap: pretty`, and `@starting-style` if you show animation — as the live examples of how
to adopt new CSS safely (`@supports`, or graceful degradation), so the lesson is a method
rather than a fixed list.

Re-check Baseline shortly before presenting: statuses drift, and `light-dark()` in particular
is due to cross to Widely around November 2026.