---
name: lcars
description: Generic, composable design rules for an LCARS-style user interface (the Star Trek TNG computer look) — palette and colour roles, condensed typography with fallbacks, the shape vocabulary (elbows, bars, pills, segmented pills, readouts), layout grammar, states, ordered colour gradients, accessibility, and rendering recipes for CSS and for immediate-mode drawing (canvas, JSFX gfx, ImGui). Technology-neutral; meant to be composed with a stack skill (e.g. `web-components`, `web-conventions`) that owns markup, components and code style. Use when designing, building, theming or reviewing an LCARS look or an "LCARS mode" for an app, plugin, dashboard or page. Triggers on "LCARS", "Star Trek UI", "TNG look", "Okudagram", "elbow frame", "LCARS theme", "LCARS mode".
---

Apply these rules to any LCARS look you design, build or review. When a composed stack skill specifies a rule (markup, component structure, token format, accessibility mechanics), the stack skill wins; this skill owns the look.

## Scope

- The visual language: colours, type, shapes, layout, states, motion.
- Rendering recipes for two families: CSS (declarative boxes) and immediate-mode drawing (rectangles and circles each frame).
- Not in this skill: component architecture, state management, framework choice, file layout — the stack skill decides.
- An LCARS look is usually an alternative theme next to a standard one. Keep layout and behaviour identical across themes; only drawing changes.

## Principles

- **Function first.** Every frame block, bar and number either groups content or labels a section. Decoration fills leftover space; it never pushes content out.
- **Black ground, flat colour.** No gradients inside shapes, no shadows, no borders, no glass, no transparency on shapes.
- **Shapes carry the colour, text stays black on them.** Coloured text appears only on the black ground.
- **Few shapes, repeated.** Elbows, bars, pills and round-ended boxes; nothing else.
- **Gaps are black and constant.** Segments are separated by the ground showing through, never by lines.

## Palette

Define the colours once as tokens; name them by role, not by hue, in code.

| Token | Hex | Role |
|---|---|---|
| `orange` | `#FF9900` | primary: frame, active state, headline text |
| `peach` | `#FFCC99` | secondary frame, secondary active state, readout values |
| `lilac` | `#CC99CC` | labels and dim text on black, frame segments |
| `periwinkle` | `#9999FF` | frame segments, navigation, zoom/scroll controls |
| `blue-grey` | `#9999CC` | inactive buttons |
| `blue-grey-hot` | `#B8B8E6` | hover on inactive buttons |
| `red` | `#CC6666` | destructive actions (delete, eject), a frame segment |
| `alert` | `#FF5555` | errors only |
| `warn` | `#FFCC33` | warnings, attention outlines |
| `ice` | `#99CCFF` | data drawn on black: waveforms, plots, meters |
| `ice-dim` | `#5E7DB8` | secondary data, overviews |
| `ground` | `#000000` | background, gaps, cut-outs |
| `well` | `#0B0B14` | data wells (plot and waveform backgrounds) |
| `empty` | `#1C1C2A` | empty slots (a pad without content) |

- At most four frame colours on one screen: orange, peach, lilac, periwinkle (plus red for one segment).
- Pick frame segment colours in a sequence that never puts the same colour next to itself.

## Ordered colours: gradient, not random

When items are numbered or ordered (pads, channels, tracks, cues), colour them along **one gradient** in their order instead of a random or rainbow palette:

- stops: `orange → peach → lilac → periwinkle`, linear interpolation per RGB channel, item `i` of `n` at `u = i / (n − 1)`;
- the colour then tells position (early items warm, late items cool), and neighbours differ only slightly — so the **number or label is the identifier, colour is the hint**; never make colour the only way to tell two items apart;
- a single-colour scheme (`peach` for all) is the calm alternative; offer it when items are not ordered.

Reference implementation (any language):

```
stops = [FF9900, FFCC99, CC99CC, 9999FF]
colour(i, n): u = i / (n - 1) * (len(stops) - 1); s = min(floor(u), len(stops) - 2); k = u - s
              return lerp(stops[s], stops[s + 1], k)   // per channel, rounded
```

## Typography

- Condensed sans, tall and narrow. First choice **Antonio** (Google Fonts, OFL). Fallback chain: `"Avenir Next Condensed"` (macOS), `"Bahnschrift Condensed"` / `Bahnschrift` (Windows 10+), `"Arial Narrow"`, then the platform sans with `font-stretch: condensed`.
- **All text upper case.** Store and pass mixed-case strings; upper-case at draw time (CSS `text-transform: uppercase`, or a draw helper), so the standard theme stays mixed case.
- Letter spacing slightly open: `0.04–0.08em`.
- Sizes: headline 36–44 px (one per screen, the app title in the top bar), section values 20–30 px, labels 12–16 px. Use weight, not size, to separate label from value.
- Numbers are tabular where they change (readouts, counters).
- When a font cannot be bundled (native plugins, canvas), detect it: a missing face silently falls back to a wide default, so measure a test string (`MMMMMMMMMM`) and accept a face only if it is clearly narrower (< 0.9×) than the platform sans at the same size.

## Shape vocabulary

| Shape | Description | Use |
|---|---|---|
| **Elbow** | a thick L: vertical column joined to a horizontal bar by a large outer radius (≈ column width / 3) and a small concave inner radius (≈ bar height) | top-left and bottom-left corners of the frame |
| **Bar** | horizontal strip, bar height 16–22 px, split into segments by gaps; the outer end round | top and bottom edges of the frame |
| **Column segment** | block in the left column, one per content section, height = that section's height | groups and labels sections |
| **Pill** | box with fully round ends (radius = height / 2) | buttons |
| **Segmented pill** | 2–4 boxes in a row with a gap; only the outer ends round | option groups (Hold / Trigger), +/− pairs, a row of pads |
| **Round-ended box** | box with round ends on the left, right, both or neither; radius capped (≈ 26 px) for tall boxes | pads, tiles, caps |
| **Cap** | narrow coloured strip (6–10 px) at the left of a dark cell | readout cells; shows which item the cell belongs to |
| **Readout** | dark `well` cell: small lilac label at the top, larger orange value below | parameters, measurements |

- Labels inside coloured shapes sit at the **bottom right**, black, with padding ≈ half the shape's height on round ends. Centre a label only when it does not fit right-aligned.
- Column segments carry a short label at their bottom right: a two-digit section number and a word (`03 CUES`).
- Decorative number groups (`47`, `1138`, `2364-B`) are allowed in leftover space only, in frame colours, never mistaken for data, and hidden from assistive technology.

## Layout grammar

- **Frame**: top elbow → top bar with the title set into a gap near the right end → left column of section segments → bottom elbow → bottom bar. Content lives in the box inside the frame.
- Column width 90–150 px; content starts one gap plus the inner radius right of the column.
- **Section alignment**: each column segment spans exactly the vertical extent of its content section; segment gaps line up with the gaps between sections. Compute segment bounds from the section positions, never hard-code them twice.
- Bars: one long primary segment (orange), one short accent segment, the title in a gap, a short end cap with a round outer end.
- Gap unit 4–6 px between segments and between buttons of a segmented pill; 10–14 px between button groups.
- Grids of tiles (pads): each row reads as one segmented bar — round outer ends on the first and last tile, square inner joins.
- Expect the frame to cost 10–20 % of width and height. Shrink flexible content (tiles, plots), not text.

## States

| State | Drawing |
|---|---|
| inactive button | `blue-grey` fill |
| hover | `blue-grey-hot` fill |
| active / on | `orange` (primary) or `peach` (secondary options) fill |
| destructive | `red` fill, always, regardless of state |
| sounding / live / playing | the item's colour blended 40–50 % towards white |
| selected tile | white 2–3 px outline inset 4–6 px from the edge |
| attention / drop target | `warn` outline |
| disabled | `empty` fill, `lilac` label |
| error text | `alert` on black |

- Never express state with transparency on shapes: round ends overlap the body, and alpha shows the overlap. Blend colours instead (`mix(colour, white|black, k)`).
- Motion, if any: instant colour changes, or a short (≤ 150 ms) step; no easing curves, no sliding.

## Accessibility

- Black text on every palette colour passes 4.5:1; orange, peach, lilac and ice text on black pass too. `blue-grey` and `periwinkle` text on black only for large text.
- Upper case and condensed type cut legibility: keep labels ≥ 12 px, values ≥ 16 px.
- Colour is never the only carrier of meaning (numbers, labels, outlines carry it too).
- Decorative frame text and number groups are hidden from assistive technology; real controls stay real buttons with their mixed-case accessible names.
- Respect a reduced-motion preference by dropping all motion.

## Rendering recipes

### CSS (declarative)

```css
:root {
  --lc-orange: #FF9900; --lc-peach: #FFCC99; --lc-lilac: #CC99CC; --lc-periwinkle: #9999FF;
  --lc-blue-grey: #9999CC; --lc-red: #CC6666; --lc-ice: #99CCFF; --lc-ground: #000;
  --lc-bar: 18px; --lc-col: 120px; --lc-gap: 4px;
  --lc-font: Antonio, "Avenir Next Condensed", "Bahnschrift Condensed", Bahnschrift, "Arial Narrow", sans-serif;
}
.lc { background: var(--lc-ground); font-family: var(--lc-font); font-stretch: condensed;
      text-transform: uppercase; letter-spacing: .05em; }
.lc-pill { border: 0; border-radius: 999px; color: #000; display: flex; align-items: flex-end;
           justify-content: flex-end; padding: 0 1em .2em; }
.lc-group { display: flex; gap: var(--lc-gap); }
.lc-group > :not(:first-child) { border-top-left-radius: 0; border-bottom-left-radius: 0; }
.lc-group > :not(:last-child)  { border-top-right-radius: 0; border-bottom-right-radius: 0; }
/* elbow: outer radius on the box, inner concave corner from a ground-coloured child */
.lc-elbow-top { background: var(--lc-orange); border-top-left-radius: calc(var(--lc-col) / 3); position: relative; }
.lc-elbow-top::after { content: ""; position: absolute; left: var(--lc-col); top: var(--lc-bar);
  width: var(--lc-bar); height: var(--lc-bar); background: radial-gradient(circle at 100% 100%,
  var(--lc-ground) calc(var(--lc-bar) - .5px), transparent var(--lc-bar)); }
```

- Build the frame with CSS grid: `grid-template-columns: var(--lc-col) 1fr`, rows for top bar, sections, bottom bar; column segments as grid items spanning their section's row so alignment is automatic.
- Upper-case with `text-transform`, never in the source text.
- Load Antonio from Google Fonts with `display=swap`; the fallback chain covers the swap period.

### Immediate mode (canvas, JSFX gfx, ImGui)

- Shapes from filled rectangles and filled circles only:
  - pill / round end: rectangle body + filled circle of radius `(h − 1) / 2` at each round end;
  - elbow outer corner: two rectangles + one quarter-filling circle of the outer radius;
  - concave inner corner: fill a square of the inner radius in the frame colour, then a ground-coloured circle of the same radius centred on the square's far corner.
- Draw order: ground, frame (including the concave cut-outs), then content — the cut-out circles would erase content drawn before them.
- No alpha on shapes (overlaps show); blend colours on the CPU.
- Upper-case strings in the draw helper, and measure the upper-cased string so right-aligned labels land where they are drawn.
- Compute the content box (`left, right, top, bottom`) once per frame per theme and lay everything out relative to it, so switching themes only changes that box and the drawing helpers.

## Review checklist

- [ ] Only palette tokens; frame colours ≤ 4 (+ red); no same-colour neighbours.
- [ ] All shapes flat, black gaps, no borders, shadows, gradients or alpha on shapes.
- [ ] Text upper case at draw time; black inside shapes, coloured only on black.
- [ ] Labels bottom-right inside shapes; section segments labelled and aligned with their sections.
- [ ] Ordered items on one gradient; items still identifiable without colour.
- [ ] Font fallback chain present; missing-font detection where the font cannot be bundled.
- [ ] Contrast checked; decorative text hidden from assistive technology.
- [ ] Standard theme untouched: same layout, same behaviour, only drawing differs.
