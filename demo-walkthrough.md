# Emerald City demo — presenter walkthrough

A feature-by-feature guide to the two files. For each item: **where to point in the
`before` file**, **what you swap it to in the `after` file**, **the one line to say**,
and the **status / gotcha** so you don't oversell it to a government audience.

The spine of the whole session: open on the `before` file ("here's the patient"),
refactor pieces live, add each one to the Delete List, and reveal the `after` file at
the end — ending on the punchline that **the after file has no JavaScript at all**.

Status key: **WIDELY** = ship it. **NEWLY** = works in current browsers, wrap in
`@supports` (or accept graceful degradation) for older ones.

---

## 1. The reset & the vendor-prefix graveyard
**Point at:** the `* { margin:0; padding:0 }` reset and the `-webkit- / -moz- / -ms-`
stacks on `border-radius`, `box-shadow`, `box-sizing`.
**Swap to:** a one-line `box-sizing` reset; unprefixed properties everywhere.
**Say:** "Half of a 2011 stylesheet was typing the same property four times. All of
these prefixes have been dead weight for years — you can just delete them."
**Status:** WIDELY. Good warm-up win — low stakes, instantly relatable, gets the
"wait, I can delete that?" reaction early.

## 2. Floats + clearfix → Grid/Flexbox + gap
**Point at:** `#content`/`#sidebar` floating, `.clearfix` everywhere, and the
`.services { margin: 0 -10px }` negative-margin gutters.
**Swap to:** `display: grid` with `gap` for the page columns; an intrinsic
`repeat(auto-fill, minmax(11rem, 1fr))` track for the cards.
**Say:** "Clearfix existed only to clean up after floats. Delete the floats and the
whole hack disappears with them — and `gap` means no more negative-margin math."
**Status:** WIDELY. **Gotcha to mention:** `gap` works in flexbox now, not just grid —
a lot of people still don't know that.

## 3. The JS "container query" + media-query ladder → `@container`
**Point at:** the jQuery `checkCardWidth()` that toggles `.is-narrow`, AND the
`max-width` breakpoints that re-flow the cards. Resize the window slowly to show the two
systems **fighting** — cards change at widths that don't line up.
**Swap to:** each `.card` is `container-type: inline-size`; a single
`@container (max-width: 12rem)` rule adapts its internals. The grid track handles the
3→2→1 reflow on its own with no breakpoints.
**Say:** "The old code asked the *window* how wide it was. But the card doesn't care
about the window — it cares about the column it's sitting in. Container queries let the
component ask about *itself*. Watch: same card, wide column versus narrow sidebar,
different layout — no JavaScript, no breakpoints."
**Status:** WIDELY (size queries). This is your **third takeaway** ("viewports →
containers"); give it room. It deletes JS *and* media queries in one move — the single
most satisfying refactor in the deck.

## 4. The min-width heading ladder → `clamp()`
**Point at:** `#hero h2` with three `@media (min-width: …)` overrides.
**Swap to:** `font-size: clamp(1.4rem, 1rem + 3vw, 2.5rem)`.
**Say:** "Four rules that only fire at magic numbers become one rule that's fluid at
every width in between."
**Status:** WIDELY. **Nice aside:** point out the `before` file mixes `min-width` for
the heading and `max-width` for the layout — that inconsistency is why hand-grown
responsive CSS rots. `clamp()` sidesteps the whole breakpoint-direction question.

## 5. Awkward heading wrap → `text-wrap: balance`
**Point at:** the long hero headline with one lonely word on the last line.
**Swap to:** `text-wrap: balance` on headings, `pretty` on paragraphs.
**Say:** "This used to be a JavaScript plugin or a hand-placed `<br>` you re-tweaked
every time the copy changed. Now it's one line, and it re-balances itself."
**Status:** NEWLY. Degrades perfectly — unsupported browsers just get normal wrapping,
so no `@supports` needed. Safe to use unconditionally.

## 6. The padding-bottom ratio hack → `aspect-ratio`
**Point at:** `.mapbox { position:relative; padding-bottom:56.25%; height:0 }` and the
absolutely-positioned child.
**Swap to:** `aspect-ratio: 16 / 9`.
**Say:** "56.25% is 9 divided by 16. Nobody should have to know that anymore."
**Status:** WIDELY. Quick, clean crowd-pleaser.

## 7. Anchor jumps under the sticky bar → `scroll-margin-top`
**Point at:** click a jump-nav link in the `before` file — the heading lands hidden
behind the sticky bar. That's a real bug your audience ships constantly.
**Swap to:** `scroll-margin-top` (or `scroll-padding-top` on the scroll container) plus
a plain `position: sticky` nav — which also deletes the scroll-position JavaScript.
**Say:** "The classic fix was an invisible pseudo-element with a negative margin. The
real fix is one property that tells the browser where to stop."
**Status:** WIDELY. Satisfying because it fixes a *bug*, not just tidies code.

## 8. `:has()` — the parent selector
**Point at:** the jQuery validation walking **up** the DOM with `.closest('.field')` to
tag the parent, because "CSS can't look upward."
**Swap to:** `.field:has(:user-invalid)`.
**Say:** "For twenty years the answer to 'style a parent based on its child' was
JavaScript. That's the entire reason this script exists. `:has()` erases it."
**Status:** WIDELY (reached Baseline end of 2023, crossed to widely in 2026). This is
your **second takeaway** ("JavaScript-saving functionality in CSS") — pair it with the
form section below for a one-two punch.

## 9. Pure-CSS form validation → `:user-invalid`
**Point at:** the rest of the validation JS — the submit handler, the manual
empty-checks, the class toggling.
**Swap to:** `required` attributes + `:user-invalid` (fires only *after* the user
interacts, unlike `:invalid` which yells on page load) + `:has()` to light up the field.
**Say:** "Add `required`, delete the script. `:user-invalid` even solves the old UX
problem where `:invalid` flagged empty fields before anyone touched them."
**Status:** WIDELY (as of May 2026). Big moment: this is where the last of the form JS
disappears.

## 10. `:focus-visible` — the accessibility fix hiding in plain sight
**Point at:** `a:focus { outline: none }`. Call it what it is: an accessibility
regression that shipped on thousands of government sites to kill the "ugly" focus ring.
**Swap to:** `:focus-visible` — ring for keyboard users, nothing for mouse clicks.
**Say:** "The designer didn't want the ring on click. Fine — but `outline: none`
removed it for keyboard users too, who *need* it. `:focus-visible` gives everyone the
right behavior." 
**Status:** WIDELY. Frame this as a **Section 508 / WCAG** win, not just aesthetics —
it lands harder with this crowd.

## 11. `accent-color` — themed checkboxes without the hack
**Point at:** the `position:absolute; left:-9999px` hidden checkbox + image-swap `.box`.
**Swap to:** a real `<input type="checkbox">` with `accent-color: var(--brand)`.
**Say:** "One property themes the native control — and you keep all the built-in
keyboard and screen-reader behavior you were throwing away with the image hack."
**Status:** WIDELY.

## 12. `field-sizing` — the auto-grow textarea
**Point at:** the jQuery `input` handler resizing the textarea by measuring
`scrollHeight`.
**Swap to:** `field-sizing: content`.
**Say:** "This is the one people have wanted for literally a decade. The textarea grows
with its content — one line of CSS, no measuring, no JavaScript."
**Status:** NEWLY (all engines as of June 2026). **Be honest here:** this is your
example of a feature that's brand-new. Show the progressive-enhancement pattern —
`@supports (field-sizing: content) { … }`, or just accept that older browsers get a
normal fixed textarea, which is a fine fallback. Great teaching moment for *how* to
adopt NEWLY features safely.

## 13. `scrollbar-color` / `scrollbar-width`
**Point at:** the `::-webkit-scrollbar` rules on the footer index — then note Firefox
ignored every one of them.
**Swap to:** `scrollbar-width: thin; scrollbar-color: <thumb> <track>`.
**Say:** "The old way was WebKit-only, so a third of your users saw a default
scrollbar. The standard properties finally work everywhere."
**Status:** NEWLY (`scrollbar-color` completed the set in Dec 2025 when Safari shipped
it). Degrades to the default scrollbar — safe without `@supports`.

## 14. jQuery slide/fade → `<details>` (and the animation story)
**Point at:** the `.slideToggle()` read-more.
**Swap to:** native `<details>`/`<summary>`.
**Say:** "Expand-and-collapse is a browser primitive now. And if you want it animated,
CSS can do that too — `@starting-style` and animating to `display: none` killed the
last reason to reach for a JS animation library."
**Status:** WIDELY (`<details>`). Mention `@starting-style` as NEWLY if someone asks
about the animation — don't rabbit-hole on it live.

---

## Architecture layer (weave through, don't silo)

These three underpin everything above and map to your **first takeaway** ("reduce
technical debt"). Rather than a separate section, call them out as you go:

- **Native nesting** — the `after` file nests every component's rules inside one block.
  "The number-one reason people kept a preprocessor. Gone." (WIDELY)
- **Custom properties + `color-mix()`** — the `before` file hardcodes the emerald green
  dozens of times with hand-computed tints. The `after` file has one `--brand` token and
  derives every shade with `color-mix()`. "Change the brand in one place. This is also
  your Sass color functions, natively." (WIDELY)
- **Cascade layers + `:where()`** — the `before` file has an over-qualified
  `body #main #content .services .card .inner .card-title` selector and two `!important`
  band-aids. The `after` file declares `@layer` order once so nothing needs to fight.
  Tie back to the `!important` discussion: **layers organize normal styles;
  `!important` still escapes them, so the goal is to remove the *need* for it.** (WIDELY)
- **Logical properties** — `margin-inline`, `padding-block`, `text-align: start`,
  `inset-block-start`. "Same code works for right-to-left and vertical scripts — which
  matters if your city serves residents in more than one language." (WIDELY)
- **`light-dark()` + `color-scheme`** — toggle your OS to dark mode and reload the
  `after` file; it flips with no extra stylesheet. (NEWLY — mention `prefers-color-scheme`
  media query as the WIDELY fallback.)

---

## Suggested live-demo order (keeps momentum)

1. Vendor prefixes + reset (easy win, sets the tone)
2. Floats → Grid + gap (biggest visual payoff)
3. `@container` (the headline — deletes JS *and* breakpoints)
4. `clamp()` + `text-wrap` (quick typography wins)
5. `aspect-ratio` + `scroll-margin-top` (two fast hack-kills)
6. `:has()` + `:user-invalid` + `accent-color` + `field-sizing` (the "forms lose all
   their JS" run — end on "the script tag is now empty")
7. `:focus-visible` (the accessibility beat — slow down here)
8. Architecture recap: nesting, tokens, layers, logical props, dark mode
9. Reveal the `after` file top to bottom → scroll to the bottom comment:
   **"No `<script>`. No jQuery."**

## Two honesty notes for a government room

- **Don't imply everything is safe everywhere.** Four items are NEWLY, not WIDELY:
  `field-sizing`, `scrollbar-color`, `text-wrap`, and `light-dark()`. Use them as your
  live examples of *how* to adopt new things — `@supports`, or a graceful fallback — so
  the takeaway is a method, not a fixed list that goes stale.
- **Re-check Baseline the week before.** Statuses drift; `:user-invalid` only reached
  widely in May 2026 and `field-sizing` in June. Do a final sweep on MDN so nothing you
  present is out of date on the day.