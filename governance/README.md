# Governance

How we govern AI tools and other product-owned software in this org. These documents codify the position drafted in [comms/2026-05-24-product-owned-tooling-heather.md](../comms/2026-05-24-product-owned-tooling-heather.md) and make it operable.

## Why these docs exist

The roadmap lists tools we are building. These documents define how they get built, shipped, and owned — and where engineering's role begins and ends. Without that, every new PM-built tool relitigates the same questions about repos, approvals, security, and incident response. We settle those once, in writing, and move on.

## The four documents

1. **[Ownership model](./ownership-model.md)** — Who owns the gate for a given tool, and why. The decision turns on who owns the end user, not on who can write the code.
2. **[Engineering guardrails](./engineering-guardrails.md)** — The platform standards, security baselines, deployment surface, and observability requirements that every product-owned tool inherits. The rails inside which product self-serves.
3. **[Approval model](./approval-model.md)** — How changes flow on product-owned repos. What product approves on its own, what triggers engineering review, what escalates to change control.
4. **[Security & data envelope](./security-data-envelope.md)** — The non-negotiable rules on data access, secrets, prod boundaries, AI model use, and incident response. These apply regardless of ownership.

## How they relate

- **Ownership** decides who is accountable.
- **Guardrails** define what they inherit by default.
- **Approval** defines how changes move.
- **Envelope** defines what no change is allowed to violate.

Ownership is the variable. Guardrails, approval rules, and the security envelope are constants set by engineering and security; product operates inside them.

## Audience

- **Product Owners and Managers** building or sponsoring AI tools listed in [ROADMAP.md](../ROADMAP.md).
- **Engineering leadership** setting and maintaining the rails.
- **Security and Legal** reviewing and updating the envelope.
- **New hires** orienting on how cross-functional tool-building works here.

## How these docs change

- All four documents are versioned in this repo. Material changes go through pull request review with engineering, product, and security as required reviewers.
- Each document carries a Version history section at the bottom. Update it on every substantive change.
- Charters are reviewed every 6 months minimum; the security envelope is reviewed quarterly.
- Exceptions to any document follow the exception process described in that document, not silent bypass.

## Status

Draft. Not yet socialized with engineering leadership. Companion to the comms draft to Heather.
