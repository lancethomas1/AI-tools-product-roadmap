# GitHub Org Structure

How the product-owned tool fleet is organized in GitHub, and how that structure relates to engineering's existing orgs.

## Purpose

Make the ownership boundary from the [ownership model](./ownership-model.md) legible at the level a developer encounters first: the repository URL. If a repo's org tells you who owns the gate, who's on-call, and who runs the support surface, ownership is operable. If it doesn't, every contributor relitigates "wait, who approves this?" on every PR.

This document defines where product-owned repos live, why they live there, and how the structure maps to engineering's existing orgs.

## Principle

**GitHub orgs are organized by who the code serves, not by who writes it.** Engineering's orgs are split by customer-facing delivery surface. Product's org is split by internal delivery audience. Same shape of question, different answer.

## The mapping

| Org | Axis | Surface / audience | Gate owner |
|---|---|---|---|
| `rcg-digital-backend` | Customer-facing delivery surface | Backend services behind guest- and employee-facing products | Engineering |
| `rcg-digital-mobile` | Customer-facing delivery surface | iOS and Android apps | Engineering |
| `rcg-digital-web` | Customer-facing delivery surface | Web apps | Engineering |
| `rcg-digital-product` | Internal delivery audience | Tools whose end users are PMs, POs, designers, and researchers inside the product org | Product |

The engineering orgs answer *"which customer surface does this code run on?"* The product org answers the same shape of question on the internal axis: *"which internal audience does this code serve?"* — one audience, one org.

## Why a separate product org, not a team or topic inside an engineering org

A few alternatives were considered and rejected:

1. **A `product-owned` GitHub team inside one of the engineering orgs.** Cheaper to set up. Rejected because the org URL is the primary signal a reader sees. If `rcg-digital-backend/prd-assistant` shows up in a PR notification, the implicit signal is "backend engineering owns this" — exactly the framing the [comms POV](../comms/2026-05-24-product-owned-tooling-heather.md) is trying to change. A team-level distinction is invisible until you click into settings.
2. **A `product` topic tag on repos in engineering orgs.** Same problem, worse — topics aren't displayed in most places URLs appear.
3. **Multiple product orgs by lifecycle stage** (drafting, planning, execution, release, post-release). Over-fragments the fleet for the volume of repos we'll have in the next 2 years, and lifecycle stages aren't a meaningful boundary for ownership, on-call, or support — they're a roadmap-organization device.
4. **A unified `rcg-digital` umbrella org with everything under it.** Would require migrating three existing engineering orgs. Not worth the cost. The four-org peer model preserves engineering's existing structure.

The four-org peer model — three engineering orgs split by surface, one product org for internal-audience tools — is the cheapest articulation that makes the gate-location visible without restructuring what already works.

## Threshold for standing it up

A separate org is overkill for a fleet of 1–2 repos. The threshold for creating `rcg-digital-product` is:

- **At least 3 product-owned repos in active development**, or
- **At least 1 product-owned repo with cross-PM contributors and a real on-call rotation**, whichever comes first.

The [roadmap](../ROADMAP.md) lists ~12 candidate tools across Now/Next/Later. The first three (PRD drafting assistant, story & ticket writer, release notes generator) are all in the Now horizon, so this threshold is expected to be crossed within the current quarter. Until then, repos may temporarily live in a personal account or a holding location, but never in an engineering org — that's the signal we're avoiding.

## What lives where

### Product org (`rcg-digital-product`)

- Every tool classified product-owned under the [ownership model](./ownership-model.md).
- Shared agent libraries and prompt assets used exclusively by product-owned tools.
- Internal documentation repos for product processes (this roadmap repo is a candidate).

### Engineering orgs

- Every tool classified engineering-owned, regardless of who sponsored it.
- Customer-facing services, even if a PM proposed the feature and shaped requirements.
- Shared platform infrastructure (CI templates, deploy tooling, observability) — these stay in engineering orgs and the product org consumes them.

### Mixed-ownership cases

Follow the ownership model's decision criteria first. The repo lives in the org of whichever function owns the gate. If a product-owned tool later reclassifies to engineering-owned (per the boundary cases in the ownership model), the repo transfers orgs as part of the reclassification — not by drift.

## Naming convention

Within `rcg-digital-product`, repo names match the tool name in the roadmap, kebab-cased:

- `prd-assistant`
- `story-ticket-writer`
- `release-notes-generator`
- `backlog-grooming-copilot`

No `product-` prefix on repo names — the org already conveys that. Avoid stage prefixes (`stage1-`, `drafting-`) — lifecycle stage is metadata, not identity, and tools migrate between stages of maturity faster than their names should change.

## Relationship to guardrails

The org boundary is about the **gate**, not the **rails**. Every repo under `rcg-digital-product` inherits the full set of [engineering guardrails](./engineering-guardrails.md):

- Same CI templates, same secret scanning, same dependency policy.
- Same deploy pipeline to the same shared platform.
- Same SSO, same observability stack, same datastores.
- Same security envelope.

What changes at the org boundary is who approves per-change diffs, who carries the pager, and who runs the support channel — per the [approval model](./approval-model.md). The platform underneath is the same one engineering runs.

This is the concrete answer to the question the comms POV anticipates: "if product has its own org, doesn't that fragment the platform?" No. The org boundary is administrative — a permissions surface and a visible signal of ownership. The platform is unified.

## Org-level settings owned by engineering

Even though product owns the gate inside `rcg-digital-product`, certain org-level settings remain owned by engineering platform / security:

- Branch protection defaults applied to new repos.
- Required status checks (the CI minimum from engineering guardrails).
- Secret scanning and dependency scanning configuration.
- SSO and SAML enforcement (inherited from the org's overall identity setup).
- Third-party app approval for the org.
- Audit log retention and access.

Product owns:

- Team membership inside the product org.
- Repo creation, archival, and naming inside the org.
- Per-repo collaborators and CODEOWNERS files.
- Repo-level branch protection above the platform default (product can require more, not less).

## Setup checklist

When `rcg-digital-product` is created:

1. Engineering platform creates the org and applies the org-level settings above.
2. Security signs off on SSO and audit configuration.
3. Product leadership is granted org owner role; engineering platform retains a co-owner seat for the platform-managed settings only.
4. The first three roadmap repos are created and seeded with the standard repo template (CI config, README skeleton, CODEOWNERS, branch protection).
5. This document is updated to reflect the actual org name and any deviations from the plan above.

## Roles

| Role | Responsibility |
|---|---|
| **Engineering platform team** | Stands up the org. Owns org-level settings listed above. Maintains the repo template. |
| **Security** | Signs off on SSO, audit, and app-approval settings. |
| **Product leadership** | Org owner. Approves new repos and team membership inside the org. |
| **Tool owner (PM)** | Owns the individual repo. Configures repo-level settings within platform defaults. |

## Escalation

If a repo's ownership is ambiguous and the [ownership model](./ownership-model.md) escalation path doesn't yield a clear answer, the repo defaults to an engineering org until classification is resolved. Moving a repo out of an engineering org requires a recorded classification decision; moving a repo into one does not.

## Review cadence

Reviewed every 6 months by product and engineering leadership, jointly. Triggers for off-cadence review:

- Fleet size crosses 10 product-owned repos (may justify sub-structure inside the product org via teams).
- A new engineering org is created or an existing one is restructured.
- A reclassification dispute exposes a gap in this document.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-26 | Lance | Initial draft. |
