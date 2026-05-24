# Ownership Model

How we decide who owns a given tool — engineering or product — and what "ownership" carries with it.

## Purpose

Settle, in advance and in writing, the question that gets asked every time a new PM-built tool comes up: "Why is product shipping code?" The answer here is not "because they can." It is "because for this tool, product owns the end user, and ownership without accountability is incoherent."

## Principle

**Ownership of the gate follows ownership of the end user.** Whoever absorbs the consequences when the tool fails — calls from customers, broken workflows, escalations, lost revenue — owns the gate that decides what ships.

This is the substantive shift from the pre-AI default. Before, "ships code" was a scarce skill, and engineering's accountability bundled with that skill by necessity. AI has unbundled the skill from the accountability. The accountability did not vanish; it just stopped being automatically located in engineering. We are now choosing where to put it deliberately.

## Definitions

- **End user.** The person whose workflow breaks first when the tool breaks. Not the buyer, not the requester, not the executive sponsor.
- **Blast radius.** The set of users, systems, and revenue affected by a tool failure. Customer-facing tools have an external blast radius. Internal tools have an internal blast radius bounded by which function uses them.
- **Product-owned tool.** A tool whose end users sit inside the product org (POs, PMs, designers, researchers) and whose failures do not reach customers or revenue-bearing employee workflows.
- **Engineering-owned tool.** A tool whose end users are customers, or whose failure cascades into customer experience or revenue.
- **Mixed-ownership tool.** A tool with end users in more than one function (e.g. a content tool used by both PMs and CS). Default to the higher-blast-radius owner; see Mixed cases below.

## Decision criteria

Apply in order. Stop at the first that gives a clear answer.

1. **Does failure reach a paying customer or guest?** If yes, engineering-owned. Non-negotiable.
2. **Does failure reach a revenue-supporting employee workflow** (sales close, CS ticket resolution, ops scheduling)? If yes, engineering-owned.
3. **Does failure reach a regulated or audited workflow** (finance, legal, HR records)? If yes, engineering-owned. Compliance accountability overrides ownership preference.
4. **Is the end user inside the product org and the blast radius bounded to product workflows?** If yes, product-owned.
5. **Mixed.** Default to engineering-owned unless the product use case dominates by a clear margin (>80% of usage) and the non-product users have a fallback. Re-evaluate at next review.

## What ownership carries

For the owning function:

- **The gate.** Approves what ships, per the [approval model](./approval-model.md), within the engineering guardrails and security envelope.
- **The on-call.** First responder when the tool breaks. Triages, communicates, escalates.
- **The support surface.** Receives user reports, runs the help channel, owns the bug backlog.
- **The roadmap.** Decides what the tool does next.
- **The metrics.** Defines and tracks success and failure indicators for the tool.

What ownership does **not** carry:

- Authority to bypass the [engineering guardrails](./engineering-guardrails.md). Those are inherited.
- Authority to bypass the [security & data envelope](./security-data-envelope.md). Same.
- Authority to claim shared infrastructure without coordination.

## Worked examples

### Example 1: PRD drafting assistant (product-owned)

- End user: PMs and POs in this org.
- Failure mode: bad first draft; PM does it manually.
- Blast radius: one PM's afternoon.
- Verdict: product-owned. Product approves changes, runs the help channel, decides the roadmap.

### Example 2: Release notes generator (mixed → product-owned)

- End users: PMs (draft); customers receive the final notes. But customers receive what the PM **edits and approves**, not what the tool produces directly.
- Failure mode: bad draft. PM catches and rewrites. No customer-facing path without PM review.
- Blast radius: PM's drafting time.
- Verdict: product-owned. The human-in-the-loop principle from the roadmap converts what looks customer-facing into an internal drafting tool.

### Example 3: Stakeholder comms tailoring (product-owned, with caveat)

- End users: PMs producing exec, sales, eng, and CS variants.
- Failure mode: tone-deaf variant goes out, requires correction.
- Blast radius: PM's reputation, occasional follow-up email. No customer impact unless a PM forwards unedited.
- Verdict: product-owned. Caveat: if usage ever evolves to auto-send to external audiences, ownership reverts to engineering.

### Example 4: Customer-facing in-app help bot (engineering-owned)

- End user: paying customers.
- Failure mode: wrong answer surfaced to customer, support escalation, brand impact.
- Blast radius: every active session.
- Verdict: engineering-owned. Product can sponsor and shape requirements; the gate sits with engineering.

### Example 5: Backlog grooming copilot (product-owned)

- End user: PMs.
- Failure mode: bad priority suggestion, PM dismisses it.
- Blast radius: one grooming session.
- Verdict: product-owned.

### Example 6: Sales-facing call summarizer (engineering-owned)

- End user: AEs closing deals; failure delays close, affects quota.
- Blast radius: revenue.
- Verdict: engineering-owned despite being "internal." Revenue-bearing employee workflows count.

## Boundary cases

- **A product-owned tool starts being used outside its intended audience.** Owner notifies engineering; usage triggers re-evaluation. Default is to harden it or transfer ownership.
- **A product-owned tool integrates with an engineering-owned system** (writes to Jira, posts to Slack channels with external members). The integration is product-owned; the system on the other side enforces its own guardrails. If the integration creates a customer-visible side effect, ownership flips.
- **An engineering-owned tool has a product-built sidecar.** The sidecar is product-owned only if it has no path to affect the parent. Otherwise it inherits engineering ownership.
- **A tool's classification changes over time.** Re-classification is a deliberate act, recorded under `governance/decisions/`. It does not happen by drift.

## Escalation

When ownership is ambiguous:

1. Tool sponsor (the PM or EM proposing the tool) drafts a one-paragraph ownership memo applying the decision criteria above.
2. Product leadership and engineering leadership review. Default to engineering ownership if disagreement is not resolved in one round.
3. Decision recorded in this repo under `governance/decisions/` (created as needed). Includes date, tool, applied criteria, and signers.

## Roles

| Role | Responsibility |
|---|---|
| **Tool sponsor** | Proposes ownership classification with rationale. |
| **Product leadership** | Approves product-owned classifications. |
| **Engineering leadership** | Approves engineering-owned classifications and any mixed-case dispositions. |
| **Security** | Consulted when classification affects the data envelope. |

## Review cadence

This document is reviewed every 6 months by product and engineering leadership. Outside of cadence, any owner can request re-review by opening an issue tagged `governance:ownership`.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-24 | Lance | Initial draft, paired with comms POV. |
