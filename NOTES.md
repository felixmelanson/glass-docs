# Notes

## Tuning

Find and replace, that's it.

| What | Where |
| --- | --- |
| Accent colour | `--gm3-sys-color-primary: #c24700` — Tabs sidebar |
| Menu bar position | `#docs-menubars { left: 1055px }` — Compact header. Tuned to my window width |
| Header scale | `transform: scale(0.65)` — Compact header |
| Icon brightness | `invert(1) brightness(0.85)` — Toolbar. Toward `0.7` if they burn |
| Indent marker colour | `hue-rotate(320deg)` — Rulers. `0deg` is stock blue, `180deg` orange |
| Glass strength | `rgba(255,255,255,0.05)` on `#docs-toolbar-wrapper`. `0` for full glass |

Anything hidden is a `display: none` block — delete the selector to get it back.
That covers Gemini, Help, Share, comments, version history, the doc star and
folder chip, the save indicator, and the ruler numbers.

## Working around Docs

Firefox-family only — the transparency needs Gecko plus a transparent window
compositor. Chromium renders the layout fine but the glass reads as flat grey.

Everything below fails quietly. No error, just a wrong pixel somewhere, so it's
written down.

**`background` shorthand eats the icons.** Docs draws its icons as inline
`background-image`. The shorthand resets `background-image`, so using it on
anything below a top-level container deletes the icon. Same for
`background-image: none`. Use `background-color` on descendants.

**`backdrop-filter` returns grey.** Over a transparent window region, Gecko
samples the opaque base canvas instead of the desktop. There is no real blur to
be had here; the glass comes from the window being transparent, not from a
filter.

**Never blanket-match `[class*="ruler"]`.** The tick marks are themselves
`.docs-ruler-face-*-division` elements, so the match eats the ruler it was
supposed to clean.

**Never fill a ruler division.** Docs draws those ticks as borders. A
`background-color` doubles them. Dimming has to happen with `opacity` on the
face.

**No negative margins in the header.** Docs measures header height and reflows
the title badges back in when it sees them move. `position: absolute` takes the
element out of that measurement, which is why the compact header works at all.

**Transparent background ≠ no border.** `#docs-chrome` held its bottom hairline
through every background pass. Borders need their own sweep.

**Pseudo-elements aren't in the DOM,** so an element sweep never reaches them.
`#kix-vertical-ruler::before` was the last white line standing.

**Panels are painted from GM3 tokens.** Clearing `background` on the element
leaves the fill coming back from a `--gm3-sys-color-*` variable further up the
tree. Override the token or it repaints on the next hover.

**Gecko has no `::-webkit-scrollbar`.** Docs also sets a non-`auto`
`scrollbar-color` on its scrollers, which is exactly why the webkit pseudos were
being ignored. Standard properties only.

**Sprites ignore `color`.** Toolbar icons are dark grey images — `invert()` to
lighten them, `hue-rotate()` for the blue indent markers. Text and highlight
swatches are real colour, so they have to be excluded from the invert or they
lie about what's selected.

---

## When it breaks

Google ships Docs UI changes constantly and the class names are unversioned, so
expect to patch selectors occasionally.

| Symptom | Cause |
| --- | --- |
| Grey slab where glass should be | `backdrop-filter` somewhere, or Transparent Zen isn't installed |
| Toolbar icons vanished | a `background` shorthand landed on a button descendant |
| Menu bar overlapping the title | `left` under **Compact header** doesn't match your window width |
| A white hairline you can't kill | it's a `::before` — add it to the pseudo-element sweep |
| Blue flashing back on hover | a GM3 token that isn't overridden |
