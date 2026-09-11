# ears-spec

> Added in this fork; not part of upstream [airails](https://github.com/AdamBien/airails).

The business-facing front door to [SBCE](https://sbce.space). It lets a product owner, business
analyst or requirement engineer write a feature request with **[EARS](https://alistairmavin.com/ears/)
requirements** as one plain Markdown file — and hand it to a development team — **without answering a
single technical question**.

## Scope

- One artifact: `<feature>.md`. Self-contained, portable, e-mailable. No repository, build tool or
  checkout needed to produce it.
- A **question policy** as the central rule: *never ask a question the author would have to ask a
  developer*. Language, framework, database, transport, deployment, module layout and test tooling
  are never raised. Technical detail the author volunteers is recorded verbatim under
  `## Constraints` — never converted into a requirement.
- The clarify loop of `/sbce new`, in business words — including the deliberate push for the
  unhappy paths (`If … then`) that authors otherwise leave unwritten.
- **Stable statement ids** (`Rn.m`) that survive revisions, so the receiving team's tests can point
  back at them.
- Modes: `new` (author), `revise` (change without renumbering), `check` (testability review before
  sending, delegated to `/ears-tests review`).

Writes nothing but the one `.md`: no code, no scaffolding, no repository changes.

## Who installs what

| Role | Skills |
|---|---|
| Product owner, business analyst, requirement engineer | `ears-spec` + `ears-tests` |
| Development team | the full set — `sbce`, `bce`, `ears-tests`, a stack skill |

The authoring side never needs `sbce`, `bce`, or any stack skill, and never sees their vocabulary.

## The handover

```
requirement engineer                    development team
--------------------                    ----------------
/ears-spec new "payment release"
/ears-tests review payment-release.md
        payment-release.md  ── e-mail ──▶  /sbce new --from payment-release.md
                                             ├─ /ears-tests review (gate)
                                             ├─ proposes the component carving, dev confirms
                                             ├─ authors the capability specs
                                             └─ freezes the file in specs/inbox/
                                           /sbce apply <bc>
```

The received file is **intake, not a source of truth**: after ingestion the team's capability specs
are authoritative, and the frozen copy in `specs/inbox/` is the record of what the business asked
for. A change is a **new revision, sent again** — never an edit on either side.

## Installing it

Requirement engineers install **only this skill and `ears-tests`** — not the other 35. From a clone
of this repository:

```
./installSpecSkills
```

No JDK, no build, no interactive terminal. It detects which agents are present on the machine and
copies the two skill folders into each of their skills directories; `-a <agent>` targets one
(`claude`, `vibe`, `kiro`, `copilot`, `codex`, `goose`), `-l` lists what is installed, `-u`
removes it, `-h` explains the rest.

`./installSkills` in the repository root installs *everything* and is the development team's
route, not this one.

## Composition

- [`ears-tests`](../ears-tests) — its `review` mode is this skill's `check`. Required.
- [`sbce`](../sbce) — the receiving end (`new --from`). Not needed to author a file, and never
  invoked from here.

## Usage

```
/ears-spec new "release a payment only after a second approval"
/ears-spec revise payment-release.md
/ears-spec check payment-release.md
```

Also triggers from intent — "write a requirements document for …", "turn these meeting notes into
acceptance criteria", "review my requirements before I send them".

The rules live in [SKILL.md](SKILL.md); the annotated file template with a worked example in
[references/feature-request-template.md](references/feature-request-template.md); how to phrase and
group statements, and the mistakes to avoid, in [references/ears-patterns.md](references/ears-patterns.md).
