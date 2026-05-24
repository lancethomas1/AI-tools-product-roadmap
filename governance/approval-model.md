# Approval Model

How changes flow on product-owned repos: what product self-serves on, what triggers engineering review, what escalates further. The operational detail behind "the per-change gate goes."

## Purpose

Replace "engineering reviews everything" with a tiered model where review weight matches change risk. Most changes on a product-owned tool are content, prompt, copy, or config edits with effectively zero blast radius. Treating those identically to a database migration is the friction the comms POV is calling out. This document defines the tiers and the rules for assigning each change to one.

## Principle

Per-change approval should reflect per-change risk. Where the risk is bounded by the engineering guardrails and security envelope, product approves on its own. Where the risk crosses those rails, the rails' owners review.

## Scope

Applies to all product-owned tools per the [ownership model](./ownership-model.md). Engineering-owned tools follow engineering's existing review processes.

## The four tiers

### T0 — Self-serve, no human review

Changes wholly inside the product-owned envelope, with zero potential to affect anything beyond the tool's own behavior for its own users.

Examples:

- Prompt edits to an AI assistant.
- Copy and microcopy.
- Adding or tweaking a template.
- Adjusting non-structural config values (model temperature, retry counts, feature flags scoped to this tool).
- Documentation in the repo.

Process: commit to a branch, open a PR, merge yourself after CI passes. No human reviewer required. CI is the gate.

### T1 — Peer review within product

Changes that affect the tool's user-visible behavior or its consumed/produced data shape but stay within the tool's own boundaries.

Examples:

- New feature or workflow inside the tool.
- New API endpoint exposed to other product-owned tools.
- Refactor of internal logic that changes outputs.
- New dependency from the approved registry.
- Adding a new template that produces a new artifact type.

Process: PR with one approving review from another PM or PO on the team. CI must pass. The reviewer is checking for clarity, correctness, and that the change stays inside its tier (i.e. not sneaking a T2 change into a T1 PR).

### T2 — Engineering review required

Changes that touch the [engineering guardrails](./engineering-guardrails.md) or that have a plausible path to affect a system outside the tool.

Examples:

- Changes to CI configuration, deploy pipeline, or infra-as-code.
- Database schema changes, especially anything touching shared tables.
- New egress destinations or external API integrations.
- New datastore or queue.
- Changes to authentication, authorization, or RBAC.
- New service-to-service integration with an engineering-owned system.
- Adding a new direct dependency that has not been vetted org-wide.

Process: PR with one approving review from a designated engineering reviewer (from the platform team or the engineering owner of the adjacent system). CI must pass. The reviewer is checking for guardrail compliance and downstream impact.

### T3 — Change control

Changes that materially affect the [security & data envelope](./security-data-envelope.md) or cross multiple functions.

Examples:

- New data class handled (e.g. tool starts processing PII for the first time).
- New AI model provider.
- Changes that affect production customer data, even read-only.
- New egress to a destination outside the standard allowlist.
- Anything an exception process is being requested for.

Process: PR plus a written change record (template under `governance/change-control/` once it exists) reviewed by engineering, security, and — where relevant — legal. Cadence is weekly; an emergency lane exists for incident-driven changes.

## How tier is assigned

The PR author proposes a tier in the PR description. Reviewers may upgrade the tier (never silently downgrade). The default if ambiguous is one tier higher than the author thinks, until precedent is set.

Concretely, the PR template (added when each repo is set up) asks:

1. Does this touch any guardrail in `governance/engineering-guardrails.md`? If yes → T2 minimum.
2. Does this touch data classified Confidential or higher, or any envelope rule? If yes → T3.
3. Does this change user-visible behavior or data shape? If yes → T1 minimum.
4. Otherwise → T0.

CI checks the PR labels and gates merge accordingly: T2 requires an `engineering-review` label that only engineering reviewers can apply; T3 requires `change-control-approved` from the change board.

## Worked examples

### Example A: Editing the PRD drafting assistant's system prompt

Tier: T0. Prompt is internal to the tool, no envelope or guardrail impact. Author merges after CI passes.

### Example B: Adding a "generate weekly digest" feature to the status synthesizer

Tier: T1. New user-visible feature, but stays inside the tool. One peer PM reviews.

### Example C: Switching the status synthesizer to read from a new Slack workspace

Tier: T2. New egress + new auth scope = guardrail-touching. Engineering reviews.

### Example D: Switching the model provider from Anthropic to a new vendor

Tier: T3. Model provider list is part of the envelope (see security & data). Change control required.

### Example E: Adding a new field to a Confluence template the assistant uses

Tier: T0 if the field is informational; T1 if it changes downstream parsing in other tools.

### Example F: Letting the release notes generator post directly to the customer changelog without PM review

Tier: T3. This change moves a tool's blast radius from internal to customer-facing — it triggers an ownership re-classification under the [ownership model](./ownership-model.md), not just an approval-tier shift.

## Emergency / hotfix path

For incident-driven changes on a product-owned tool:

- T0 and T1 changes proceed normally.
- T2 hotfixes can proceed with verbal engineering approval recorded in the PR within 24 hours, followed by retroactive engineering review within 48 hours.
- T3 changes do not have an emergency self-serve path. The incident commander engages on-call from engineering and security directly.

This path is for outages and customer-impacting bugs, not for "I want to ship faster."

## Audit trail

- Every merged PR records: author, tier, reviewers, CI status, and (for T2+) the linked review or change record.
- Audit logs are retained per the standard platform retention policy.
- Quarterly, the engineering platform team samples a random N% of T0/T1 merges per repo to check tier-assignment accuracy. Systematic under-tiering by a repo or author is corrected with feedback, not punishment.

## Roles

| Role | Responsibility |
|---|---|
| **PR author** | Proposes tier, assembles required reviewers, lands the change. |
| **Peer PM/PO reviewer** | Reviews T1 changes for clarity and tier accuracy. |
| **Engineering reviewer** | Reviews T2 changes for guardrail compliance. |
| **Change control board** | Reviews T3 changes weekly. Engineering + security; legal as needed. |
| **Tool owner** | Accountable for the tool's overall quality regardless of tier mix. |

## Review cadence

Reviewed every 6 months. Tier definitions evolve as we see real PRs; expect adjustments in the first two quarters.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-24 | Lance | Initial draft. |
