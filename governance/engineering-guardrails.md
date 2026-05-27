# Engineering Guardrails

The platform standards, security baselines, deployment surface, and observability requirements that apply to every product-owned tool. Re-sectioned into a **Tier A baseline** (achievable today on substrate product already has) and a **Tier B layer** (requires engineering platform investment). See [`strategy/engineering-optional.md`](../strategy/engineering-optional.md) for the rationale and tier model.

## Purpose

Make explicit which rails survive the ownership shift, and which rails depend on engineering resourcing that has not yet been committed. The [comms POV to Heather](../comms/2026-05-24-product-owned-tooling-heather.md) concedes that engineering owns platform standards, security guardrails, and deployment surface. That concession holds. What this revised document adds is honesty about which of those engineering currently provides and which are aspirational pending eng investment — and how product ships in the meantime.

## Principle

The rails exist to make it safe for non-engineering owners to ship code. Some of those rails exist today on substrate product can use without further engineering work (Tier A). Others require engineering to fund and maintain (Tier B). Product builds at Tier A by default and requests Tier B per-tool when a specific tool's scope demands it.

## Scope

These guardrails apply to every repository in the product-owned tool fleet — every tool classified product-owned under the [ownership model](./ownership-model.md). They also apply to engineering-owned tools, which have additional engineering-internal standards on top.

## How the tiers work per guardrail

Each numbered section below splits Tier A and Tier B. A tool may operate entirely at Tier A; that is the default. A tool whose scope requires a Tier B guardrail flips its tier per the trigger process in [`strategy/engineering-optional.md`](../strategy/engineering-optional.md).

### 1. Source control

**Tier A (today).**

- All product-owned tools live in repos under `rcg-digital-product`, never in personal accounts.
- `main` branch is protected: required PR, required passing checks, required at least one approving review (per the [approval model](./approval-model.md), the reviewer may be a peer PM).
- Force-pushing to `main` is disabled.
- Secrets are never committed; GitHub's native secret scanning is enabled on every repo.
- Dependency advisories from GitHub are enabled and routed to the repo owner.

**Tier B (requires engineering investment).**

- Commit signing enforcement org-wide.
- Custom branch-protection policies beyond GitHub defaults.
- A repo template factory that scaffolds new product-owned repos against an engineering-maintained baseline.

### 2. CI / build

**Tier A (today).** Every repo has GitHub Actions configured before its first deployment, running:

- Linting on the language(s) in use.
- Type checking where applicable.
- A test job on every PR.
- GitHub's secret scanning.
- GitHub's dependency vulnerability advisories.
- A license check on new dependencies (manual or via a workflow action).

**Tier B (requires engineering investment).**

- Engineering-maintained CI templates inherited across the product fleet, so per-repo CI config is minimal.
- Centralized SCA / SBOM generation across the fleet.
- Required-status-check enforcement at the org level beyond per-repo configuration.

### 3. Dependencies

**Tier A (today).**

- Direct dependencies come from the public registries (npm, PyPI, GitHub).
- License and CVE checks happen per-repo via GitHub's native tooling and a workflow action.
- AI model providers are restricted to the approved list maintained in the [security & data envelope](./security-data-envelope.md). Adding a provider is a T3 change.

**Tier B (requires engineering investment).**

- Org-mirrored npm/PyPI registries with proactive curation.
- Fleet-level dependency-graph analysis and policy enforcement.
- An internal package repository for shared product-owned libraries.

### 4. Deployment surface

**Tier A (today).**

- Interactive tools (skills in `pm-toolkit`) run on champion laptops; deployment is `git pull`.
- Batch agents run on GitHub Actions on cron, webhook, or `repository_dispatch` triggers.
- "Deploy to prod" for an agent is "merge to `main`" — the workflow on `main` is the prod surface.
- Promotion environments are not provided at Tier A: agents have one environment (the production GitHub Actions run on `main`). Testing happens via PR runs and a separate workflow file for ad hoc test invocation.

**Tier B (requires engineering investment).**

- A shared K8s/serverless deploy surface for long-running services that exceed GitHub Actions' runtime, memory, or concurrency limits.
- A standard CD pipeline with `dev` / `staging` / `prod` promotion and pipeline-gated environment moves.
- Infrastructure-as-code templates for product-owned services.

### 5. Identity, auth, and access

**Tier A (today).**

- Tool users authenticate to SaaS surfaces (Jira, Confluence, Slack) via the SaaS's own OAuth, scoped per integration.
- Champions authenticate to GitHub via the org's existing GitHub SSO.
- Service identities for batch agents use GitHub Actions secrets and per-SaaS OAuth tokens.
- Role-based access uses GitHub team membership for repo / workflow access, and SaaS-native roles for SaaS surfaces.

**Tier B (requires engineering investment).**

- Org SSO in front of any custom (non-SaaS) web surface a product-owned tool exposes.
- A central RBAC service for cross-tool authorization beyond what GitHub teams and SaaS roles can express.
- Service-account provisioning with rotation SLAs and audit, beyond what GitHub Actions secrets provide.

### 6. Observability

**Tier A (today).**

- Batch agents log to GitHub Actions run history.
- Every batch agent posts a structured heartbeat (run id, item counts, error count) to `#pm-toolkit-ops` on every run. Missing heartbeats are noticed by humans, not paged.
- Champions track their interactive sessions through Anthropic's console.
- A dashboard, when one exists, is a markdown table in the repo README pointing at the relevant SaaS surfaces.

**Tier B (requires engineering investment).**

- Centralized structured-logging backend with PII redaction at ingest.
- Metric scraping (request, error, latency) and a real metrics backend.
- Paging on alert thresholds, routed to an on-call rotation.
- Dashboards in a shared observability tool.

### 7. Data storage

**Tier A (today).**

- Product-owned tools do not stand up their own datastores. State lives in Jira (tickets), Confluence (docs), GitHub (code and issues), Slack (channels), and Linear if used.
- "Schema" is whatever a Jira custom field or Confluence template imposes.
- Backups are the SaaS providers' responsibility.

**Tier B (requires engineering investment).**

- A shared Postgres cluster with per-tool schemas for tools needing persistent state we own.
- Shared object storage for artifacts that don't fit in SaaS.
- A shared queue for cross-tool async work.
- Backup, retention, and recovery operations across the fleet.

### 8. Networking

**Tier A (today).**

- Network egress is whatever GitHub Actions runners and champion laptops can reach. SaaS APIs (Jira, Confluence, Slack, Anthropic, etc.) are reachable via the public internet.
- Inbound network access for batch agents is via GitHub's webhook delivery and `repository_dispatch`; no custom inbound surfaces.

**Tier B (requires engineering investment).**

- An egress proxy with allowlist, so non-SaaS destinations can be reached safely.
- A standard inbound surface for tools that need to receive traffic from outside GitHub's webhook delivery.
- IP allowlisting on SaaS surfaces tied to corporate egress IPs.

## How guardrails change

The guardrails change when engineering decides they should change. Product can request a change by opening an issue tagged `governance:guardrails` describing the friction and the proposal. Engineering responds within two weeks, either updating the guardrail, rejecting with rationale, or scheduling for the next review.

Tier-B-only guardrails additionally change when engineering commits resourcing. A new Tier B capability lands in this document with an effective date and a list of tools it unlocks.

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
| **Engineering platform team** | Owns the Tier B rails. Maintains anything that requires shared infrastructure or fleet-wide enforcement. |
| **Security** | Co-owns guardrails that touch the envelope, at both tiers. |
| **Tool owner (PM)** | Operates within Tier A guardrails. Surfaces friction. Triages alerts. Requests Tier B per-tool when scope demands. |
| **Engineering reviewers** | Approve guardrail-touching changes per the approval model. |

## Review cadence

Reviewed every 6 months by the engineering platform team, with input from product owners on friction points. Tier A is also reviewed informally any time GitHub or a SaaS provider changes a capability that affects the substrate.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-24 | Lance | Initial draft. |
| 0.2 | 2026-05-27 | Lance | Re-sectioned into Tier A baseline and Tier B (requires eng platform). Paired with [`strategy/engineering-optional.md`](../strategy/engineering-optional.md). |
