# Layout contract

What the script guarantees about the page, so a reader can trust the picture and a maintainer can
change it safely. All of this lives in `scripts/onepager`; this file is the explanation, not a
second source of truth.

## The sheet

A single canvas, 1660 units wide, height computed from the content:

```
40 ........................ 940 | 990 ..................... 1620
+---------------------------+   +--------------------------+
| title, one-liner, stamp                                  |   y 40..130
+---------------------------+   +--------------------------+
| derived drawing           |   | EARS statements          |   y 150..
|   one row per group Rn    |   |   one block per group Rn |
+---------------------------+   +--------------------------+
| legend                    |                                  bottom
```

The HTML scales the whole canvas to A4 landscape and caps it at 194 mm tall, so a one-pager stays
one page — a large spec gets smaller type rather than a second sheet.

## EARS pattern → shape

The system is the BC (capability spec) or "the system" (feature request); either way the subject is
dropped and only the condition and response are drawn.

| Pattern | Contributes |
|---|---|
| Event-driven `When <trigger>` | the happy path — its response is the green outcome box |
| Complex `While <pre>, when <trigger>` | same as event-driven; the precondition is not drawn separately |
| State-driven `While <pre>` | same as event-driven |
| Optional-feature `Where <feature>` | same as event-driven |
| Unwanted-behaviour `If <trigger>, then` | a yellow guard diamond (the trigger) plus a red box (the response) |
| Ubiquitous `The BC shall …` | no shape — nothing triggers it; it appears in the right column only |

A group is drawn as: operation ellipse → guard₁ → guard₂ → guard₃ → outcome. Guards beyond three
are listed on the right and announced under the row; a group with no happy statement gets a grey
"no positive outcome declared" box instead of a green one.

## Geometry

Colours are named roles, not hexes — the role-to-palette mapping is in `palette.md`.

| | |
|---|---|
| operation ellipse | 140 × 54, blue on blue tint |
| guard diamond | 132 wide, height grows with the text, amber on amber tint |
| rejection box | 130 × 46, red on red tint |
| outcome box | 168 × 54, green on green tint (grey on grey tint when absent) |
| group chip and connector | one of six hues, cycled per group |
| header rule | blue, under the title block; a hairline rule closes the page above the footer |

A diamond's usable width narrows towards its points, so its text wraps to **62 %** of the bounding
box and the shape grows taller rather than letting text escape — capped at five lines, then
truncated with `…`. This is the single most common source of ugly generated diagrams; keep it.

Rows are spaced from the tallest diamond in the row, never a fixed constant, so a long condition
cannot push a rejection box into the row below.

## Excalidraw elements emitted

`rectangle`, `ellipse`, `diamond`, `arrow` and `text` — nothing exotic, no images, no libraries.
Labelled shapes emit a container plus a bound `text` element (`containerId` set, container's
`boundElements` pointing back). Statement-id tags and the right-hand column are standalone text.
Arrows carry relative `points` and `roundness: {"type": 2}`; dashed connectors are arrows with
`strokeStyle: "dashed"` and no arrowhead.

Written against the published Excalidraw file format. Not derived from any third-party generator.

## Determinism

Every element id is `sha256(kind@x,y,index)` truncated to 22 hex characters, and `seed` /
`versionNonce` derive from the same hash. Nothing is timestamped. Same input → byte-identical
output, so regeneration produces a reviewable diff instead of a whole-file churn. **Keep it that
way**: no `Math.random()`, no `Instant.now()`, no map iteration order that depends on hashing.

## Extending it

- A new shape kind: add a case to `excalidraw()` **and** to `svg()` — the two emitters read the same
  `Shape` list, and a kind handled by only one produces a picture that disagrees with itself.
- A colour change: edit the **role** block at the top of the script, never a call site. A hex written
  inline is a brand change that the next one will miss.
- A new input format: extend `parseSpec`; keep `readBody` as the single place that strips `///`.
- New relation wording for `--system` edges: extend `edgeOf`, and keep the rule that an unrecognised
  bullet is **reported, not guessed** — a wrong arrow is worse than a missing one.
