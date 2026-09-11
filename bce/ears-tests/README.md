# ears-tests

An [AIrails.dev](https://airails.dev) skill that generates **parameterized (table-driven) tests
from [EARS](https://alistairmavin.com/ears/) requirement statements** — the deterministic transform
behind a [SBCE](https://sbce.space) spec's `## Requirements`.

## Scope

- The EARS→test mapping: requirement **group `Rn`** → one parameterized test method; **statement
  `Rn.m`** → one labeled row.
- The EARS-pattern → row-shape table (which column is arrange/act/assert, and the row's happy/unhappy role).
- Preserving the **spec↔test trace** — the `Rn.m` id stays grep-visible as the row's display name.
- The line between what is generated mechanically (structure, ids, roles, the bijection check) and
  what stays authored (fixtures, `<response>` assertions).
- **`review`** *(added in this fork)* — the same mapping read backwards: read EARS
  statements from a plain Markdown file and report, per statement id, whether each could become a
  row. Generates no code, edits nothing, and asks nothing about the stack or the repository — so it
  runs for a requirement engineer who has neither. Checks, severities and report format:
  [references/review-checklist.md](references/review-checklist.md).

Stack-neutral. No runner, framework verb, or test syntax — those come from the composed stack skill.

## Composition

- [`sbce`](../sbce) — provides the spec and the stable `Rn.m` ids this skill consumes.
- [`bce`](../bce) — where the BC and its tests live.
- a **stack skill** — the test syntax and the green/red oracle:
  [`microprofile-server`](../microprofile-server) (JUnit 5 `@ParameterizedTest`),
  [`java-cli-app`](../java-cli-app) (zunit), or `web-components` (Playwright).
- [`ears-onepager`](../ears-onepager) — renders the same statements as a
  one-pager. Anything `review` flags also draws badly, so it runs first.
- [`ears-spec`](../ears-spec) — the business-facing authoring skill; its `check` is this skill's
  `review`. Only these two are installed on the requirement-engineering side, so `review` may never
  depend on a stack skill or on `sbce` being present.

Per-stack realization examples live in [references/realizations.md](references/realizations.md).

## Usage

Activated when turning EARS requirements — or a `/sbce` capability spec's `## Requirements` — into
tests. A requirement group with ≥2 statements becomes one parameterized test; each statement `Rn.m`
becomes a row whose display name embeds its id. The rules in [SKILL.md](SKILL.md) drive the mapping;
the composed stack skill supplies the syntax.

To check statements instead of generating tests:

```
/ears-tests review payment-release.md
```

Also triggers from intent — "are these requirements testable", "review my requirements", "check
these EARS statements".
