# Engineering Guardrails

The platform standards, security baselines, deployment surface, and observability requirements that every product-owned tool inherits by default. Engineering owns these. Product builds inside them.

## Purpose

Make explicit the rails that survive the ownership shift. The comms POV concedes — fully — that engineering owns platform standards, security guardrails, and deployment surface. This document spells out what that means in concrete terms so that "self-serve within the rails" is not vacuous.

## Principle

The rails exist to make it safe for non-engineering owners to ship code. They are not negotiable per-change. They are negotiable as a body, on a slow cadence, owned by engineering. Product's job is to operate inside them and surface friction back to engineering when the rails are wrong.

## Scope

These guardrails apply to every repository in the product-owned tool fleet — every tool classified product-owned under the [ownership model](./ownership-model.md). They also apply to engineering-owned tools, which have additional engineering-internal standards on top.

## The guardrails

### 1. Source control

- All product-owned tools live in repos under the org GitHub account, never in personal accounts.
- `main` branch is protected: required PR, required passing checks, required at least one approving review (per the [approval model](./approval-model.md), the reviewer may be a peer PM).
- Force-pushing to `main` is disabled.
- Commit signing is enabled where infra allows; not blocking otherwise.
- Secrets are never committed; pre-commit secret scanning is enabled on every repo.

### 2. CI / build

Every product-owned repo has CI configured before its first deployment. The minimum CI suite:

- Linting on the language(s) in use (e.g. ESLint, Ruff).
- Type checking where applicable.
- A test job that runs on every PR. Coverage threshold is set per repo; the floor is "this exists and runs."
- Secret scanning on every push.
- Dependency vulnerability scanning, with criticals blocking merge.
- License scanning for any open-source dependency added.

CI configuration files are reviewed by engineering when the repo is set up. Subsequent changes to CI config require engineering review (see [approval model](./approval-model.md), tier T2).

### 3. Dependencies

- Direct dependencies are pulled from approved registries only (the org's npm/PyPI mirrors, not arbitrary URLs).
- A dependency added for the first time across the product-owned fleet goes through a one-time engineering review for license, maintenance status, and known CVEs. Subsequent additions of the same dependency in other repos are self-serve.
- AI model providers are restricted to the approved list, maintained by engineering and security. Adding a new provider is an envelope change; see [security & data envelope](./security-data-envelope.md).

### 4. Deployment surface

- Product-owned tools deploy to the shared platform (the same Kubernetes/serverless surface engineering uses). They do not stand up their own infrastructure.
- Deployment is via the standard CD pipeline; manual deploys to shared environments are not permitted.
- Environments: `dev`, `staging`, `prod`. Promotion is gated by passing CI. Product owners can promote to `dev` and `staging` self-serve. Promotion to `prod` requires the change to be in `main` (which already required PR review) and follows the standard pipeline; no second human approval is required *unless* the change touches a guardrail (T2/T3 per approval model).
- No tool may bypass the CD pipeline to push artifacts to `prod`.

### 5. Identity, auth, and access

- All tools use the org SSO for authentication. No standalone user databases.
- Role-based access control follows the org's existing roles; tools do not invent new role hierarchies without engineering review.
- Service accounts and API tokens are provisioned through the standard secrets infrastructure. No long-lived tokens checked into config; no shared accounts.

### 6. Observability

Every product-owned tool, before its first prod deploy, has:

- Structured logging to the standard log aggregation backend, with PII redaction in place.
- Standard request, error, and latency metrics exposed and scraped.
- A minimum alert: error rate above threshold pages the owner (a PM, per the ownership model). Alert routing is configured at repo setup.
- A dashboard linked in the repo README.

The owner is responsible for triaging alerts. Engineering's responsibility is that the observability stack itself works.

### 7. Data storage

- Product-owned tools use sanctioned datastores only: the shared Postgres cluster (with a dedicated schema per tool), the shared object store, and the shared queue. No new datastores without engineering review.
- Schema changes within a tool's own schema are self-serve. Schema changes that touch cross-tool or shared tables require engineering review.
- Backups are inherited from the platform; product-owned tools do not run their own backup tooling.

### 8. Networking

- All inter-service traffic stays on the org's internal network. Egress to third-party APIs goes through the standard egress proxy with allowlisting.
- New egress destinations require engineering review (typically a one-day turn).

## How guardrails change

The guardrails change when engineering decides they should change. Product can request a change by opening an issue tagged `governance:guardrails` describing the friction and the proposal. Engineering responds within two weeks, either updating the guardrail, rejecting with rationale, or scheduling for the next review.

## Exceptions

An exception to any guardrail above requires:

1. A written exception request in this repo under `governance/exceptions/` (created as needed).
2. Approval by engineering leadership and, where the security envelope is implicated, security.
3. A scoped duration (typically 90 days) and a remediation plan to return to compliance.
4. The exception recorded in the relevant repo's README so future maintainers see it.

Exceptions are not a workaround mechanism. They are a recorded, time-bound deviation with an end date.

## Roles

| Role | Responsibility |
|---|---|
| **Engineering platform team** | Owns the rails. Maintains CI templates, deployment surface, observability stack, datastores. |
| **Security** | Co-owns guardrails that touch the envelope. |
| **Tool owner (PM)** | Operates within guardrails. Surfaces friction. Triages alerts. |
| **Engineering reviewers** | Approve guardrail-touching changes per the approval model. |

## Review cadence

Reviewed every 6 months by the engineering platform team, with input from product owners on friction points. Material changes go to PR with cross-functional review.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-24 | Lance | Initial draft. |
