# Feature request template

One file per feature, named `<slug>.md`. Self-contained: it must be readable by someone who has
the file and nothing else — no repository, no ticket system, no meeting memory.

Rules for filling it in:

- **Business only.** No language, framework, database, protocol, deployment, module or class names anywhere. If the author states one anyway, it goes under `## Constraints` verbatim — never into a requirement.
- **The subject of every statement is "the system".** The author does not decide how the team carves the feature into components; the team rewrites the subject on intake.
- **Every requirement is an EARS statement** with a stable id `Rn.m` — see `ears-patterns.md`. One trigger, one response, per statement.
- **Ids are stable across revisions.** Never renumber on reorder. A withdrawn statement's id is retired, not reused; a changed statement keeps its id.
- **`## Constraints` and `## Open questions` carry no `Rn` ids** — they are not requirements and are never tested. Constraints use `Cn`, open questions `Qn`, both stable across revisions so the receiving team's intake map can name them.
- **Keep `## Out of scope`, even when empty.** It is what stops the team from inventing the boundary.
- Optional `_(why: …)_` on a statement: the rule's *origin* — a regulation, an incident, a policy. Terse. It is never tested, and never describes how anything works today.

## The file

```markdown
---
feature: payment-release
title: Payment Release with Dual Approval
author: A. Muster, Requirement Engineering
contact: a.muster@example.com
date: 2026-08-25
revision: 1
status: ready
---

# Payment Release with Dual Approval
> Let a payment captured by a client advisor reach the payment network only after a second, independent approval.

## Context
<!-- optional; why this is wanted, what happens today. Background for the reader — never a requirement -->
Payments above an advisor's own limit are approved today by e-mail, which leaves no reliable audit record.

## Operations
<!-- what someone can do; one plain verb-noun line each; no screens, no systems -->
- `submit-payment` — a client advisor submits a captured payment for release
- `approve-payment` — a second, authorised person approves a submitted payment
- `reject-payment` — a second, authorised person rejects a submitted payment, with a reason

## Requirements
<!-- EARS statements, grouped under a titled Rn, each with a stable id Rn.m; every operation is covered by a group -->
### R1: Submit a payment for release
- R1.1 — When a client advisor submits a captured payment, the system shall record it as pending approval.
- R1.2 — If the payment names no beneficiary account, then the system shall refuse the submission.
- R1.3 — If the submitting advisor is not authorised for the payment's currency, then the system shall refuse the submission. _(why: currency authorisations are granted per advisor by Compliance)_

### R2: Approve a payment
- R2.1 — While a payment is pending approval, when an authorised approver approves it, the system shall release it to the payment network.
- R2.2 — If the approver is the person who submitted the payment, then the system shall refuse the approval. _(why: four-eyes principle, mandatory under the internal control framework)_
- R2.3 — If the payment is no longer pending approval, then the system shall refuse the approval.

### R3: Reject a payment
- R3.1 — While a payment is pending approval, when an authorised approver rejects it with a reason, the system shall record the rejection together with its reason.
- R3.2 — If a rejection carries no reason, then the system shall refuse it.

### R4: Record who acted
- R4.1 — The system shall record, for every payment, who submitted, approved or rejected it, and when.

## Terms
<!-- optional; the business nouns used above, one line each; no fields, no data types -->
- Payment — an instruction to transfer money, captured but not yet sent.
- Pending approval — submitted, and neither approved nor rejected yet.
- Approver — a person authorised to approve payments, up to a stated limit.

## Constraints
<!-- optional; externally imposed facts the team must live with. Not requirements: id Cn, never tested -->
- C1 — Approver authorisations come from the existing entitlement system; this feature does not manage them.
- C2 — Go-live must precede the next external audit.

## Out of scope
<!-- what this feature deliberately does not do; keep the heading even when empty -->
- Approving several payments in one action.
- Changing a payment after submission — it is rejected and re-captured instead.

## Open questions
<!-- optional; unresolved points, id Qn, each naming who decides. The team must not guess -->
- Q1 — May an approver approve a payment they submitted when no other approver is available? _(Compliance to decide)_

## Revisions
<!-- optional; what changed since the previous revision, by id -->
- r1 — initial version
```

## Frontmatter fields

| Field | Meaning |
|---|---|
| `feature` | stable lowercase slug; survives title changes and is what the team's traceability refers back to |
| `title` | human title, free wording |
| `author` | name and role of whoever is answerable for the content |
| `contact` | where the team sends questions |
| `date` | date of this revision |
| `revision` | integer, raised on every version sent out |
| `status` | `draft` (circulating for comment) or `ready` (buildable) |
