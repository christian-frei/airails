# Palette

The colours the one-pager draws with, and — more importantly — the indirection between them and
the layout code. Nothing in the layout refers to a hex; it refers to a **role**, and the roles map
to the palette in one block at the top of `scripts/onepager`.

## Palette

Excalidraw's own element colours: one dark tone for strokes and text, one tint for fills.

| Name | Dark | Tint |
|---|---|---|
| blue | `#1971c2` | `#a5d8ff` |
| amber | `#f08c00` | `#ffec99` |
| red | `#e03131` | `#ffc9c9` |
| green | `#2f9e44` | `#b2f2bb` |
| grey | `#868e96` | `#e9ecef` |
| violet | `#6741d9` | — |
| teal | `#0c8599` | — |
| magenta | `#c2255c` | — |
| orange | `#e8590c` | — |
| charcoal | `#1e1e1e` | — |
| slate | `#495057` | — |
| hairline | — | `#ced4da` |

## Roles

| Role | Colour | Where |
|---|---|---|
| title | charcoal | page title |
| ink | charcoal | statement text, labels inside shapes |
| secondary text | slate | the one-line responsibility, BC one-liners, system edges |
| muted | grey | stamp line, legend labels, footer |
| paper | white | chip and BC-node fill |
| operation | blue on blue tint | the boundary operation ellipse |
| guard | amber on amber tint | an `If…then` trigger diamond |
| refused | red on red tint | an `If…then` response box, and its arrow |
| outcome | green on green tint | the happy statement's response box |
| absent outcome | grey on grey tint | a group with no happy statement |
| group hues (6, cycled) | blue, violet, magenta, teal, orange, green | group chip, right-column header and ids, the dashed connector between them |
| rules | blue under the header, hairline above the footer | page structure |

## Rebranding

Replace the palette values with your own and every output follows — that is the whole point of the
role indirection. Two constraints worth keeping:

- **Group hues carry text on white.** They must stay dark enough to read at 12–15 px. Pale tones
  belong in fills, never in the hue cycle.
- **Every fill is paired with a darker stroke from the same family.** Shapes read as shapes because
  of that contrast, not because of the outline weight.

A brand change should touch only the palette block. If you find yourself editing a hex at a call
site in the layout code, add a role instead — the next rebrand will miss anything inline.

## Finish

Output is drawn flat (`roughness: 0`, sans face) so the `.excalidraw` file and the printed PDF look
like the same document. `--sketch` restores Excalidraw's hand-drawn look (`roughness: 1`,
Excalifont) for a workshop or a draft review.

No typeface is embedded: Excalidraw ships its own font set, and the HTML uses the system sans
stack. Embedding a licensed face would put a font binary in the repository — a licensing question
this skill does not need to answer.
