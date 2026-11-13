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
- Modes: `new` (author), `revise` (change without renumbering), `check` (stack-free testability
  review before sending).

Writes nothing but the one `.md`: no code, no scaffolding, no repository changes.

## Who installs what

| Role | Skills |
|---|---|
| Product owner, business analyst, requirement engineer | `ears-spec` |
| Development team | a stack bundle plus the `sbce` bundle — `sbce`, `capability-brief`, `ears-tests`, `bce`, … |

The authoring side never needs `sbce`, `bce`, or any stack skill, and never sees their vocabulary.

## The handover

```
requirement engineer                    development team
--------------------                    ----------------
/ears-spec new "payment release"
/ears-spec check payment-release.md
        payment-release.md  ── e-mail ──▶  /capability-brief review payment-release.md
                                             └─ one brief per capability, dev confirms
                                           /sbce new <brief>
                                             └─ authors the capability spec
                                           /sbce apply <bc>
```

The received file is **input, not a source of truth**: once the team has authored its capability
specs they are authoritative, and the file stays the record of what the business asked for. A
change is a **new revision, sent again** — never an edit on either side.

## Installing it

Requirement engineers install **only this skill** — not the other fifty. From a clone
of this repository:

```
./installSpecSkills
```

No JDK, no build, no interactive terminal. It detects which agents are present on the machine and
copies the skill folder into each of their skills directories; `-a <agent>` targets one
(`claude`, `vibe`, `kiro`, `copilot`, `codex`, `goose`), `-l` lists what is installed, `-u`
removes it, `-h` explains the rest.

`./installSkills` in the repository root installs *everything* and is the development team's
route, not this one.

## Composition

- [`capability-brief`](../capability-brief) and [`sbce`](../sbce) — the receiving end. Not needed
  to author a file, and never invoked from here.

## Usage

```
/ears-spec new "release a payment only after a second approval"
/ears-spec revise payment-release.md
/ears-spec check payment-release.md
```

Also triggers from intent — "write a requirements document for …", "turn these meeting notes into
acceptance criteria", "review my requirements before I send them".

The rules live in [SKILL.md](SKILL.md); the `check` codes and report format in
[references/review-checklist.md](references/review-checklist.md); the annotated file template with a worked example in
[references/feature-request-template.md](references/feature-request-template.md); how to phrase and
group statements, and the mistakes to avoid, in [references/ears-patterns.md](references/ears-patterns.md).
