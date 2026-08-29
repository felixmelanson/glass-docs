# Glass Docs — Zen Boost

A glassmorphism theme for Google Docs. Docs stops painting its own chrome, so
whatever is behind the browser window shows through and only the page itself
stays opaque.

Built for **Zen Browser** (Firefox/Gecko) using the built-in **Boost** custom-CSS
editor. Accent: `#c24700`.

<!-- screenshot goes here -->

## Features

- Glassmorphism across the entire editor chrome — header, toolbar, rulers,
  sidebar, right rail
- Compact top bar: the menu bar moves up onto the title row, nothing is lost
- Gemini and the rest of the upsell cluster removed
- Customisable accent colour and a small set of tuning knobs (below)
- Tick marks on the rulers survive; the numbers don't
- Thin, near-invisible Gecko scrollbars

## What it hides

| Removed | Why |
| --- | --- |
| Gemini menu, Help menu | noise |
| Share, quick actions, version history, comments, sidekick | doesn't earn the header space |
| Doc star, folder chip, save indicator, title label layer | the label layer is what makes the title ghost |
| Ruler numbers | ticks are enough |
| Recommendation / upsell entry points | noise |

Everything hidden is `display: none` in one place per group — delete the
selector to get it back.

## Installation

1. Enable **Boost** for `docs.google.com`.
2. Open the Boost `{ }` code editor and paste the contents of
   [`glass-docs.css`](glass-docs.css).
3. Reload the doc.

For the darker, fully transparent look, install the
[Transparent Zen mod](https://zen-browser.app/mods/). It does **not** have to be
enabled on Google Docs — it just needs to be installed so the window region is
transparent.

## Customising

Everything below is a plain find-and-replace in `glass-docs.css`.

**Accent colour** — the GM3 token block under **Tabs sidebar**,
`--gm3-sys-color-primary`. Replace `#c24700`.

**Menu bar position** — **Compact header**, `#docs-menubars { left: 1055px }`.
Tuned to one window width; nudge it until the menu bar sits where you want it on
the title row. `top: -40px` controls the vertical lift.

**Header scale** — **Compact header**,
`#docs-branding-container { transform: scale(0.65) }`.

**Toolbar icon brightness** — **Toolbar**, `filter: invert(1) brightness(0.85)`.
Drop toward `0.7` if the icons burn against a light wallpaper.

**Indent / tab marker colour** — **Rulers**, `hue-rotate(320deg)`. The markers
are blue sprites, so the hue gets rotated rather than set. `0deg`/`360deg` is the
original blue, `180deg` lands on orange. Delete the block to revert.

**Surface tint** — the `rgba(255, 255, 255, 0.05)` on `#docs-toolbar-wrapper`
is the only real fill in the theme. Raise it if the
toolbar disappears into a busy wallpaper, drop it to `0` for full glass.

## Gotchas

Hard-won, and all of them will silently break the theme:

1. **Docs draws icons as inline `background-image`.** The `background`
   shorthand resets `background-image`, so using it on a descendant deletes the
   icon. Same for `background-image: none`. Use `background-color` on anything
   below the top-level containers.
2. **Never blanket-match `[class*="ruler"]`.** The tick marks are themselves
   `.docs-ruler-face-*-division` elements, so the match eats the ruler it was
   supposed to clean.
3. **Never set `background-color` on a ruler division.** Docs draws those ticks
   as borders; a fill doubles them.
4. **No `backdrop-filter` over a transparent window region.** Gecko samples the
   opaque base canvas instead of the desktop and returns solid grey.
5. **Never move header parts with negative margins.** Docs measures header
   height and reflows the title badges when it sees them. `position: absolute`
   takes the element out of that measurement.
6. **Transparent background is not the same as no border.** `#docs-chrome` kept
   its bottom border through every background pass.
7. **Pseudo-elements are not in the DOM**, so element sweeps never reach them.
   `#kix-vertical-ruler::before` was the last white line standing.
8. **Docs paints panels from GM3 tokens.** Clearing `background` on the element
   alone leaves the fill coming back from a `--gm3-sys-color-*` variable further
   up the tree — override the token too.
9. **Gecko has no `::-webkit-scrollbar`.** Docs sets a non-`auto`
   `scrollbar-color` on its scrollers, which is exactly why the webkit pseudos
   were being ignored. Standard properties only.

## Section map

`glass-docs.css` runs in this order — the border and pseudo-element sweeps come
first so later blocks can put borders back on purpose (toolbar separators,
sidebar edge) instead of fighting a sweep that runs after them.

Root · Sweep: borders · Sweep: pseudo-elements · Header · Compact header ·
Toolbar · Omnibox · Rulers · Canvas and page · Scrollbars · Tabs sidebar ·
Right rail

## Troubleshooting

**A grey slab where glass should be.** Something is applying `backdrop-filter`,
or the Transparent Zen mod isn't installed. See gotcha 4.

**Toolbar icons vanished.** A `background` shorthand landed on a descendant of a
button. See gotcha 1.

**Menu bar overlapping the title.** Adjust `left` under **Compact header** for your
window width.

**A white hairline you can't kill.** It's probably a `::before`. Add it to the
pseudo-element sweep near the top.

**Docs shipped a redesign and half of this stopped working.** Class names are
unversioned and Google changes them without notice. Inspect the offending
element and add the new selector to the matching section.

## Compatibility

Firefox-family browsers only — the transparency depends on Gecko plus a
transparent window compositor. Chromium builds render the layout fine but the
"glass" reads as flat grey.

Google ships Docs UI changes continuously; expect to patch selectors
occasionally.

## Licence

MIT — see [LICENSE](LICENSE).
