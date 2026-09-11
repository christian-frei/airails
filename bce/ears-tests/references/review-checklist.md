# review — checks, severities, report format

The checks a `review` run performs, each with a stable code so the report can cite it and an author
can look it up. Review reads Markdown and emits a report — nothing else.

**Two shapes of input, one set of checks.** A business feature request (`/ears-spec`) has
`## Operations` and the subject "the system"; an SBCE capability spec has `## Boundary` and the
subject is the BC. Detect which, and apply the same checks either way.

**Severities.** `blocker` — the statement cannot become a row as written. `warn` — a row is
possible, but weak, ambiguous or incomplete. `info` — worth knowing, nothing to fix.

## Structure

| Code | Check | Severity |
|---|---|---|
| S1 | The statement matches exactly one of the six EARS templates. Name the matched pattern in the report; no match is a blocker | blocker |
| S2 | The modal verb is `shall`. `should` / `may` / `can` introduce a gradation the binary oracle cannot express | blocker |
| S2b | `must` / `will` — mandatory but off-grammar; the fix is mechanical | warn |
| S3 | Every statement carries an id `Rn.m`, well formed and unique in the file | blocker |
| S4 | Every group is a titled `### Rn: <title>`, and its statements share its `n` | blocker |
| S5 | One subject throughout the file — "the system", or the BC. A statement whose subject is a person or an external system constrains the wrong thing | warn |
| S6 | Gaps in a group's numbering. Legitimate after a retirement — report, never "fix" | info |

## Testability

| Code | Check | Severity |
|---|---|---|
| T1 | The response is observable from outside. Flag words that name no observable outcome: *quickly, fast, promptly, timely, efficiently, appropriately, properly, correctly, as needed, if necessary, where relevant, user-friendly, intuitive, robust, reliable, secure, scalable, seamless, easily, sufficient, reasonable, optimal, etc., and so on* | blocker |
| T2 | One response per statement. Responses chained with *and* / *or* pass and fail independently — they are separate rows | warn |
| T3 | One trigger per statement. A compound condition either splits into two statements or gets named as one business condition | warn |
| T4 | A `While <precondition>` names a state that can be set up from outside. An unobservable precondition cannot be arranged, so the row cannot be built | warn |
| T5 | Every group whose operation accepts input has at least one `If … then` statement. Reported against the **group**, not a statement — its rejection path would go untested | warn |
| T6 | No hidden enumeration. *invalid, incorrect, wrong, not allowed, unsuitable* stand in for a list of rules; each rule is its own statement with its own id | warn |
| T7 | No *how* in a statement — *table, schema, database, index, endpoint, REST, HTTP, API, JSON, XML, queue, topic, cache, thread, class, service, file format*. In a feature request, suggest moving it to `## Constraints`; in a capability spec, it is spec-format drift | warn |
| T8 | A ubiquitous statement (`The system shall …`) is a single case, not a table — a plain test is the right shape | info |
| T9 | A `shall not` response is only testable against the cases someone enumerates. Prefer naming the observable outcome ("shall refuse the approval") | info |

## Coverage

Only when the file has an operations section (`## Operations` / `## Boundary`):

| Code | Check | Severity |
|---|---|---|
| X1 | Every operation is covered by at least one requirement group | warn |
| X2 | Every requirement group traces to an operation. A group of only ubiquitous statements — a cross-cutting obligation such as record-keeping or retention — legitimately traces to none: report it as `info`, not `warn` | warn |
| X3 | Business nouns used in statements are defined, when the file has a `## Terms` section | info |

## Report format

Blockers first, then warnings, then info; group-level findings after statement-level ones. One line
per finding: severity, id, check code, what is wrong, and a concrete suggestion. Close with a count
and a verdict.

```
payment-release.md — 12 statements in 4 groups, feature request

blocker  R2.4  T1  "promptly" names no observable outcome
                   → "shall release it on the same banking day"
blocker  R3.3  S2  "should" — a requirement is mandatory or it is not a requirement
                   → "shall"
warn     R1.1  T2  two responses in one statement ("record it and notify the advisor")
                   → split; the second becomes R1.4
warn     R3    T5  no "If … then" statement — the rejection path of `reject-payment`
                   would go untested
info     R4.1  T8  ubiquitous statement — a single test case, not a table

10 of 12 statements can become a test row. 2 blockers, 2 warnings, 1 info.
Not ready to send: resolve the blockers.
```

With nothing to report:

```
payment-release.md — 12 statements in 4 groups, feature request
No findings. All 12 statements can become a test row.
```

- **Never rewrite the file.** Suggestions are text in the report; the author decides.
- **Never invent the missing content.** T1's suggested wording is a *shape* ("name the deadline"),
  not a fabricated value — review has no way to know the business rule.
- A finding that needs a business decision is not a defect to fix here: say so, and point at the
  file's `## Open questions`.
