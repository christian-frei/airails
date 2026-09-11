---
name: ears-spec
description: Author a business-readable feature request as a plain Markdown file with EARS requirements — for product owners, business analysts and requirement engineers who hand the file to a development team by e-mail, ticket or repository. Asks business questions only: never about programming languages, frameworks, stacks, databases, transports, deployment, test tooling or code layout. Produces one self-contained `.md` per feature, which the receiving team ingests with `/sbce new --from`. Composes with `/ears-tests review` for a testability check before sending. Use when writing, structuring, revising or handing over a feature request, requirements document or acceptance criteria — with or without a codebase in reach. Triggers on "feature request", "requirements document", "business requirements", "acceptance criteria", "EARS requirements", "requirement engineering", "product owner spec", "spec for the developers", "hand over to the dev team", "user story with acceptance criteria", "write requirements", "review my requirements".
---

Author one feature request as a plain Markdown file that a development team can build from —
without asking the author a single technical question. Invoke as `/ears-spec <mode> <feature>`
(`new`, `revise` or `check`), or let it trigger from intent.

The artifact is **one `.md` file per feature**. It is self-contained and portable: an e-mail
attachment, a ticket body, or a file in a folder. No repository, build tool or checkout is
required to produce it.

## Guiding principles

- **The author owns the *what*; the team owns the *how*.** Every question this skill asks must be answerable by someone who has never seen the code.
- **Requirements are [EARS](https://alistairmavin.com/ears/) statements** — one of six patterns, each testable, each carrying a stable id. Prose that cannot be phrased as a statement is context, not a requirement.
- **The file is intake, not a contract-of-record.** Once the team ingests it, their capability specs become the source of truth; this file is frozen as the record of what the business asked for. It is never kept in sync afterwards — a change means a **new revision, sent again**.
- **Nothing is guessed.** An unresolved point goes to `## Open questions`, never into an invented requirement. The team must not have to guess either.
- **Ids are stable across revisions** — never renumbered on reorder; a withdrawn statement's id is retired, not reused. The team's traceability points back at these ids.

## Question policy — the core rule

> Never ask a question the author would have to ask a developer to answer.

**Ask about** (business):

- who or what triggers an operation, and when
- what the business expects to happen on success, stated so an outsider could observe it
- what must happen when input is wrong, missing, late, duplicated or unauthorised
- which *states* change the answer (captured / pending / approved / settled / closed)
- the data the business cares about: which fields exist, which are mandatory, which identify the thing, which are validated and against which rule
- thresholds, limits and amounts **as business rules** ("above CHF 100 000 a second approval is required")
- who is allowed to do it, and what must be recorded for audit
- regulatory, contractual or internal-control obligations the feature must satisfy
- what is deliberately out of scope
- how the business will recognise that it works

**Never ask about** (technical — the team decides these):

- programming language, framework, library, stack, or product selection
- database, storage, schema, table, index, migration
- transport or protocol — REST, HTTP, API, queue, batch file, event
- deployment, environment, infrastructure, hosting, scaling
- module, package, class, service or repository layout, and the names of any of these
- test frameworks, test kinds, or how anything will be verified
- engineering performance budgets (latency, threads, caching). A **business** deadline is a requirement — "the payment shall be released on the same banking day"; a millisecond budget is not

**When the author volunteers technical detail** — "it must feed Avaloq", "we already use Kafka" —
record it verbatim under `## Constraints`, give it the next `Cn` id, and move on. Never turn it into a requirement, never
ask a follow-up about it, never validate or expand it. It is input for the team, not for this file's logic.

## Clarify loop

The same rigour as any spec interview, in business words only:

- **Loop, don't stop at one round.** Each answer opens new gaps; re-derive and re-ask until nothing is left open.
- **One ambiguity per question**, specific over generic, with enumerable options and an "other" escape. Skip what the author already answered.
- **Never assume silently.** Lean on a default only by naming it — "I'd assume a rejected payment cannot be re-approved; confirm or correct."
- **Interrogate every operation**: its trigger and expected outcome; every way it can go wrong (`If…then`); every state that changes its answer (`While…`); the data it needs and which parts are mandatory.
- **Cover the unhappy path explicitly.** Authors state the success case unprompted and the failure cases never. An operation that accepts input needs at least one `If…then` statement.
- **Stop only when** another requirement engineer, reading the file alone, would write the same statements. If in doubt, ask once more.
- Accept any input shape — a user story ("As a … I want …"), an e-mail thread, meeting notes, a screenshot description — and convert it. Never demand a particular input format.

## Modes

### new — author a feature request

1. Take the feature name or description. Derive a stable slug (`payment-release`) and a human title.
2. Run the clarify loop until every operation, statement, term and boundary is settled.
3. Write the file from `references/feature-request-template.md` into the current directory as `<slug>.md`, unless the author names a location.
4. Number the statements: group `Rn` per operation-sized theme, statement `Rn.m` within it.
5. Run `check` (below) and resolve what it reports.
6. Print the handover note (see **Sending it**).

### revise — change an existing feature request

1. Read the existing file; keep every id it already carries.
2. Clarify only what changed. **Never renumber.** New statements take the next free id in their group; withdrawn statements are removed and their id retired, never reused; changed statements keep their id.
3. Raise `revision:` in the frontmatter and append a `## Revisions` line naming what changed, by id.
4. Run `check` and print the handover note.

### check — testability review before sending

Delegate to `/ears-tests review <file>` — the stack-free review that reports, per statement id,
whether it can become a test: pattern match, measurable response, one trigger per statement,
missing rejection paths, duplicate or missing ids. It generates no code and asks no technical
questions. Fix what it flags, or move the point to `## Open questions` if it needs a business
decision. If `ears-tests` is not installed, ask for it — do not improvise a partial check.

## The file

Full annotated template with a worked example: `references/feature-request-template.md`.
The six EARS patterns, when to reach for each, and the common mistakes: `references/ears-patterns.md`.

| Section | Holds | Required |
|---|---|---|
| frontmatter | `feature` slug, `title`, `author`, `contact`, `date`, `revision`, `status` | yes |
| `# Title` + `>` line | one sentence: what the feature lets the business do | yes |
| `## Context` | why now, what happens today. Background — never a requirement | no |
| `## Operations` | what someone can do; one plain verb-noun line each | yes |
| `## Requirements` | EARS statements, grouped `Rn`, each with a stable id `Rn.m` | yes |
| `## Terms` | the business nouns used above, one line each | no |
| `## Constraints` | externally imposed facts to live with, id `Cn`. Never an EARS statement, never tested | no |
| `## Out of scope` | what this feature deliberately does not do. Keep the heading even when empty | yes |
| `## Open questions` | unresolved points, `Qn`. The team must not guess | no |
| `## Revisions` | what changed since the previous revision, by id | no |

- **The subject of every statement is "the system"** — the author does not know, and must not invent, how the team will carve the feature up. The receiving team rewrites the subject when it maps statements onto its components.
- **One language per file.** English is the default; if the file is written in another working language, keep the six EARS keywords and `shall` in English so `/ears-tests review` can still read it.
- No screens, no field layouts, no wireframes. A requirement about *what must be captured* is business; a requirement about *where the button sits* is design and belongs elsewhere.

## Sending it

Before the file leaves, confirm all of these — and say plainly which ones fail:

- `status: ready` (a `draft` is fine to circulate, but say so).
- `/ears-tests review` reports no blockers.
- Every operation in `## Operations` is covered by at least one requirement group.
- Every operation that accepts input has at least one `If…then` statement.
- `## Open questions` is empty, or every entry names who must decide it.

Then hand over the file itself — attachment, ticket body, or commit. Tell the recipient it is a
`/sbce new --from <file>` intake document, and that questions come back to the `contact:` address
quoting statement ids.

## What the team does with it (context for the author)

The team runs `/sbce new --from <file>`. It reviews the statements, proposes how the feature
splits into components, confirms that split with a developer, and authors the resulting capability
specs into the codebase; each statement becomes at least one test that carries its id. The
original file is kept, frozen, as the record of the request. The author never sees any of that
structure — but knows the ids survive, which is why they are stable.

## Composition

- **`/ears-tests`** — the `review` mode used by `check`. Required.
- **`/sbce`** — the receiving end (`new --from`). Not needed to author a file, and never invoked by this skill.
- This skill writes **nothing but the one `.md`**: no code, no scaffolding, no repository changes, no diagrams.
