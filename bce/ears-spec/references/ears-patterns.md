# Writing EARS statements

[EARS](https://alistairmavin.com/ears/) (Easy Approach to Requirements Syntax) gives a requirement
one of six shapes. The shape is not decoration: it forces the author to say *when* something
applies, *what* triggers it, and *what* must then be observable — the three things a team needs and
that free prose reliably omits.

Every statement:

- uses **`shall`** — never *should*, *may*, *must*, *will*, *can*. There is no gradation: a
  statement is mandatory and will be tested, or it is not a requirement. Optionality is expressed
  with the `Where` pattern, not with a weaker verb.
- names **one trigger and one response**. Two responses joined by "and" are two statements.
- has **"the system"** as its subject — the author does not decide how the team splits the work up.
- is **observable from outside**. If nobody could tell from the outside whether it happened, it
  cannot be tested and it is not yet a requirement.

## The six patterns

| Pattern | Template | Use it for |
|---|---|---|
| Ubiquitous | `The system shall <response>.` | something always true, with no trigger and no state — an ever-present obligation |
| Event-driven | `When <trigger>, the system shall <response>.` | the normal case: something happens, the system answers |
| Unwanted behaviour | `If <trigger>, then the system shall <response>.` | the error case: bad, missing, late, duplicated or unauthorised input |
| State-driven | `While <precondition>, the system shall <response>.` | something that holds only for as long as a state lasts |
| Optional feature | `Where <feature is included>, the system shall <response>.` | behaviour that only exists in some configurations, markets or products |
| Complex | `While <precondition>, when <trigger>, the system shall <response>.` | a trigger whose answer depends on the state the thing is in |

## Choosing the pattern

Read the sentence you want to write and ask, in order:

1. Does it only apply in a certain **state**? → `While …` — and if a trigger is also involved, `While …, when …`.
2. Is it about something **going wrong** — invalid, missing, unauthorised, out of sequence, too late, duplicated? → `If … then`.
3. Is it a reaction to something **happening**? → `When …`.
4. Does it exist only when an **option** is present? → `Where …`.
5. None of these — it simply always holds? → `The system shall …`.

**Reach for `If … then` deliberately.** Authors write the success case unprompted and the failure
cases almost never — and the failure cases are where systems actually hurt. Every operation that
accepts input deserves at least one, usually several: what if it is empty, unauthorised, duplicated,
already done, or arrives in the wrong state?

## Grouping and ids

- Group statements that concern **one operation** under a titled `### Rn: <title>`.
- Give every statement an id `Rn.m` — group `n`, statement `m` — written first on the line.
- Ids are **stable across revisions**: never renumbered on reorder; a withdrawn id is retired, not
  reused. The team's tests carry these ids, so a reused id silently re-points a test at a different rule.
- A group holding one lone statement is fine. So is a group with eight.

## Common mistakes

| Written | Why it fails | Instead |
|---|---|---|
| The system shall process payments quickly. | "Quickly" cannot be observed — nobody can say whether it happened. | When a payment is approved, the system shall release it on the same banking day. |
| The system should validate the IBAN. | *Should* leaves it optional; there is no such thing. | If the beneficiary IBAN is not valid, then the system shall refuse the submission. |
| When a payment is approved, the system shall release it and notify the advisor and write an audit entry. | Three responses in one statement — they can pass and fail independently. | Three statements, one response each. |
| The system shall store the payment in the payments table. | *Table* is a technical decision the team owns. | When a client advisor submits a payment, the system shall record it as pending approval. |
| The user shall enter a reason. | The subject must be the system — a requirement constrains the system, not the person. | If a rejection carries no reason, then the system shall refuse it. |
| The system shall be secure and user-friendly. | Not a requirement — no trigger, no observable response. | Break it into concrete rules, or move it to `## Context`. |
| When the payment is invalid, the system shall reject it. | "Invalid" hides an unstated list of rules — each rule is its own statement. | One `If … then` per validation rule, each naming the specific defect. |

## What is *not* a requirement

Genuine and useful, but they belong in other sections and carry no id and no test:

- **Background, motivation, history** → `## Context`
- **Externally imposed facts** — an existing system to integrate with, a deadline, a regulation cited as such → `## Constraints`
- **Explicit non-goals** → `## Out of scope`
- **Anything undecided** → `## Open questions`, with the name of whoever decides
- **Why a rule exists** → a terse `_(why: …)_` at the end of the statement it belongs to
