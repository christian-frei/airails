---
name: ears-onepager
description: Turn an EARS spec into a one-pager — a derived process drawing on the left, the EARS statements on the right, linked by requirement id and colour — as an editable `.excalidraw` file, a print-ready `.html` and a `.pdf`. Works on an `/ears-spec` feature request, an `/sbce` capability spec (`package-info.java` / `package-info.md`), or a whole source tree. Generation is a bundled zero-dependency Java script, so the diagram costs no output tokens and is byte-identical on re-run. Use when a spec needs to be shown to people rather than read — a review, a steering meeting, a handover, a wall print. Triggers on "one-pager", "onepager", "excalidraw", "diagram of the spec", "visualise the requirements", "draw the process", "picture of this capability", "print the spec", "pdf of the requirements", "show this to the business", "requirements poster".
---

Render an EARS spec as a one-pager. The drawing is **derived from the statements** — nothing is
hand-placed, so it is correct by construction and regenerates cleanly after every spec change.

## Run the script — never author the JSON

```
bce/ears-onepager/scripts/onepager <spec-file>     [-o <dir>] [--pdf] [--footer <text>] [--sketch]
bce/ears-onepager/scripts/onepager --system <root> [-o <dir>] [--pdf] [--footer <text>] [--sketch]
```

Zero dependencies, JDK 25+. It always writes `<name>.excalidraw` and `<name>.html`; `--pdf` prints
the HTML with the first headless browser it finds (`CHROME` env var, Chrome, Chromium, Edge).
Without one, tell the user to open the HTML and use Print → Save as PDF.

`--footer` sets the line under the closing rule — classification, owner, distribution. Offer it for
anything leaving the team; never invent a classification label yourself.

**Never write Excalidraw JSON yourself.** A one-pager is ~100 elements and tens of kilobytes of
coordinates and hashes — deterministic work that belongs in the script. Hand-authoring it burns
thousands of output tokens and produces subtly malformed files that drift on every regeneration.
If the script cannot express something, change the script, not the output.

## Inputs

| Input | Mode |
|---|---|
| an `/ears-spec` feature request (`payment-release.md`) | capability |
| an `/sbce` capability spec (`package-info.java` with `///`, or `package-info.md`) | capability |
| a source root holding several package docs | `--system` |

The base package's doc is taken as the system doc; every other `package-info.*` is a BC. `///`
prefixes are stripped, frontmatter is read, and trailing `_(why: …)_` rationale markers are dropped
before rendering.

## What the drawing says

One row per requirement group, read left to right:

| Shape | Comes from |
|---|---|
| light blue ellipse | the group's boundary operation |
| orange diamond | an `If…then` statement's trigger — the guard |
| red box below it | that statement's response — the rejection path |
| green box at the end | the happy statement's response (`When` / `While` / `While…when`) |
| warm grey box at the end | **no happy statement in the group** — a real gap, shown rather than hidden |

Every shape carries the statement id it came from, the right-hand column repeats it, and the group
colour plus a dashed connector tie the two sides together. In `--system` mode the left side is the
BC map: nodes are BCs, edges are the system doc's declared `## Components` wiring — direction read
from the relation wording (`may call`, `is reached only via`), never guessed from the order the
names appear in. A bullet with no recognised relation word is reported and left undrawn.

## After generating — the part that needs judgment

The script cannot tell whether the picture is *right*. Read the output and report:

- A **grey outcome box**: that group has no happy statement. Say so — it is a spec gap, not a drawing bug.
- A **truncated diamond** (`…`): the condition is too long to sit inside the shape. The full text is in the right column, but a condition needing five lines is usually two statements — offer to split it, and hand it to `/ears-tests review`.
- **`+n more If…then statement(s)`**: a group with more than three guards is drawn to three. The rest are listed on the right; say which were not drawn.
- An operation label reading like a group title means the spec's `## Boundary` / `## Operations` and its `## Requirements` groups don't line up one-to-one. Report the mismatch; don't paper over it.
- More than one printed page: the sheet is scaled to fit A4 landscape, so a very large spec gets small. Suggest `--system` for the overview plus one page per capability.

## Palette and finish

Colours are assigned by **role** — operation, guard, refused, outcome, six cycled group hues — and
the roles map to the palette in one block at the top of the script, so rebranding is a single edit
rather than a sweep through call sites. Palette and role table: `references/palette.md`.

Output is drawn flat (`roughness: 0`, sans face) so the `.excalidraw` and the printed PDF look like
the same document. `--sketch` restores the hand-drawn Excalidraw look for a workshop or a draft.

## Regeneration, not editing

The `.excalidraw` file is generated **wholesale** on every run: same input, byte-identical output —
element ids are content hashes and nothing is timestamped, so a spec change produces a readable
diff. Manual edits in Excalidraw are therefore lost on the next run. That is deliberate: the spec
is the source of truth, and a drawing that can disagree with it is worse than no drawing. Point
anyone who wants a different picture at the spec, or at the script.

## Composition

- **`/ears-spec`** and **`/sbce`** own the specs this reads. It never edits them.
- **`/ears-tests`** owns whether a statement is testable; this owns whether it is *showable*. Run `review` first — a statement it flags will also draw badly.
- **`/mermaid`**, **`/drawio`**, **`/bce-diagrams`** are for hand-authored architecture diagrams. This one is not hand-authored, and is the only one bound to EARS ids.

Layout contract, colours and how to extend the script: `references/layout.md`.
