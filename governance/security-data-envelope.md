# Security & Data Envelope

The non-negotiable rules on data access, secrets, prod boundaries, AI model use, and incident response. These rules apply to every product-owned tool regardless of ownership classification.

## Purpose

The comms POV concedes that prod access, shared infra, and sensitive data sit inside engineering's guardrails and are not opened up by the ownership shift. This document is the concrete list of what "inside the envelope" means. It is the layer that does not move when ownership moves.

## Principle

Some constraints exist to protect the company and its customers, not to manage day-to-day workflow. Those constraints do not get traded against velocity. They are inherited by every tool, recorded here, and changed only through the change control tier of the [approval model](./approval-model.md).

## Scope

Every product-owned tool. Every engineering-owned tool. Every tool sponsored by anyone in this org that handles org data.

## Data classification

Tools inherit the org's data classification:

| Class | Examples | Default handling |
|---|---|---|
| **Public** | Marketing copy, published release notes. | No restrictions. |
| **Internal** | Roadmaps, internal docs, ticket text without customer identifiers. | Standard repo access, SSO-gated. |
| **Confidential** | Strategy docs, unannounced features, employee data, financials. | Restricted access, audit-logged. AI tools require T2 review before processing. |
| **Restricted / Regulated** | Customer PII, PHI, payment data, secrets, security incident details. | Not processed by product-owned tools at all without T3 review and a written DPIA-equivalent. |

The PM proposing or maintaining a tool is responsible for knowing the highest data class their tool touches and recording it in the repo README.

## Data handling rules

1. **No customer PII in product-owned tools without T3 review.** Customer email, name, support ticket content, and behavioral data flow into a product-owned tool only when the change control board approves and a data-handling note is written.
2. **No production database writes from product-owned tools.** Read access is granted via read-replicas or sanctioned APIs, scoped to the data the tool actually needs.
3. **Synthetic and anonymized data in dev/staging.** Production data is not copied into non-prod environments. Engineering provides anonymized fixtures.
4. **Data minimization.** Each tool stores only the data needed for its function. Logs scrub PII at ingest, not at query time.
5. **Retention.** Tool-specific data follows the org retention policy unless a shorter retention is set per tool. No tool sets a longer retention without legal review.

## AI / LLM-specific rules

These rules layer on top of the general data handling rules. They exist because LLMs introduce failure modes — prompt injection, training-data leakage, hallucinated PII — that traditional data rules do not cover.

1. **Approved providers only.** The approved model-provider list is maintained by engineering and security. Adding a provider is a T3 change.
2. **No training on internal data.** Every approved provider contract includes a no-training clause for our data. Tools must use API endpoints that respect this; this is verified at provider onboarding, not per-tool.
3. **Prompt injection awareness.** Tools that read untrusted text (customer comms, Slack threads, ticket bodies) and act on it must implement either output review by a human (the human-in-the-loop principle from the roadmap) or a constrained tool surface that limits what the model can do.
4. **Citations for synthesis.** Tools that synthesize internal context produce citations to source documents, per the roadmap operating principle. This is also an envelope rule because it bounds the impact of hallucinated content.
5. **No agent autonomy across the envelope.** A tool that takes autonomous action (sends, posts, writes) does so only within data classes it is approved for. A tool approved for Internal cannot, on its own, post to a Public channel.

## Production boundaries

1. **No direct prod access from product-owned tools.** No prod database connections from PM-owned scripts, no prod-only API tokens in PM repos, no test-in-prod patterns.
2. **Shared infrastructure is sanctioned, not extended.** Product-owned tools use the shared K8s cluster, the shared Postgres, the shared queue. They do not stand up parallel infra.
3. **Egress allowlist.** Tools call out to the network only through the sanctioned egress proxy. Adding a destination is a T2 change at minimum, T3 if the destination handles regulated data.
4. **No long-lived secrets in repos or local files.** Secrets come from the vault. Secret scanning is a CI gate on every repo. A leaked secret triggers immediate rotation and an incident review.

## Secrets management

- All secrets (API keys, service tokens, model provider keys) live in the org secrets manager.
- Repos pull secrets at deploy time. They never appear in source, environment files, or local `.env` checked into git.
- Rotation is automated where supported; manual rotation cadence is quarterly minimum.

## Identity and access

- SSO is the only authentication path for tool users. No invented user databases.
- Service accounts for tool-to-tool calls follow the principle of least privilege: scoped tokens, short lifetimes, no shared accounts.
- Role assignment for restricted data uses the org's existing data access roles. New roles require T3 review.

## Incident response

When a product-owned tool causes or contributes to an incident:

1. **First responder** is the tool owner (the PM, per ownership model). They follow the standard incident runbook: declare, communicate, contain.
2. **If the incident touches anything in this envelope** — data exposure, prod-impacting outage, regulated data involvement — the tool owner immediately escalates to security on-call and engineering on-call. The PM remains incident commander unless escalation is required.
3. **Postmortem** is required for any envelope-touching incident. Written, blameless, published in the standard incident channel. Includes corrective actions with owners and dates.
4. **Repeat envelope incidents** on the same tool trigger an ownership re-review. A tool that keeps tripping the envelope is a candidate for re-classification under the ownership model.

## How the envelope changes

Envelope changes are slow and intentional. The process:

1. Proposed change documented in `governance/change-control/` with rationale, affected tools, and risk analysis.
2. Reviewed by engineering leadership, security, and — for any change touching regulated data — legal.
3. If approved, this document is updated via PR with the change reflected and the version history incremented.
4. Affected tool owners are notified and given a defined window to comply.

The envelope is not negotiated per-tool, per-PR, or per-PM. It is negotiated as policy, on a slow cadence, with the right reviewers.

## Exceptions

A time-bound exception to an envelope rule requires:

- A written exception request in `governance/exceptions/`.
- Approval from engineering leadership, security, and (for regulated data) legal.
- A maximum duration of 90 days, renewable only once with re-review.
- A remediation plan to return to compliance, with an owner and a date.

Exceptions are rare. The default answer to "can we skip this rule?" is no.

## Roles

| Role | Responsibility |
|---|---|
| **Security** | Owns the envelope. Approves T3 changes that affect it. |
| **Engineering leadership** | Co-owns the envelope where it intersects platform. |
| **Legal** | Reviews regulated-data changes and retention policy changes. |
| **Tool owner (PM)** | Knows their tool's data class, complies with envelope rules, escalates incidents per protocol. |
| **Incident commander** | The PM by default; escalated to security on-call for envelope-touching incidents. |

## Review cadence

Reviewed quarterly by security and engineering leadership. The envelope is the most frequently-reviewed governance doc because the threat and regulatory landscape moves fastest.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-24 | Lance | Initial draft. |
