# Engineering-Optional Delivery: Shipping Without a Platform Commitment

How the roadmap, governance, and build approach change when engineering's platform investment is treated as an enhancement rather than a precondition. A working position on shipping at a self-sufficient baseline today, with a defined upgrade path if and when engineering resources arrive.

## Purpose

The existing governance assumes engineering is funding a shared platform — CI templates, deploy pipeline, observability stack, shared datastores, egress proxy, secrets infra, SSO. Every tool inherits that. The roadmap silently depends on it. If engineering doesn't resource that platform, the roadmap doesn't ship.

That dependency is not currently safe to bet on. This document redesigns the delivery model so the dependency is severable: a baseline tier (Tier A) that ships on substrate already available to product, and an enhancement tier (Tier B) that activates as engineering investments land.

## Principle

**Engineering platform contributions are an enhancement tier, not a precondition.** Define the smallest substrate product can deliver on today without any new engineering commitment, build to that, and accept the consequences honestly. Reserve the rest as a prioritized menu engineering can fund when they're ready.

This is not adversarial. It's the only way to make the roadmap something the product org actually owns — accountable for delivery — instead of something we pretend to own while waiting on someone else.

## Tier A — Self-Sufficient Baseline

The substrate that exists today, requiring zero net-new engineering work:

| Layer | Tier A choice | What it depends on |
|---|---|---|
| Source control | Repos under `rcg-digital-product` on the org GitHub | GitHub seat (already paid) |
| Runtime (interactive) | Claude Code on champion laptops | Champion seats per [licensed-seats](./licensed-seats.md) |
| Runtime (batch / agentic) | GitHub Actions on cron, webhook, and `repository_dispatch` | GitHub Actions minutes (already paid) |
| State | Jira, Confluence, Linear, Slack, GitHub | SaaS already contracted and admin'd |
| Auth into SaaS | Per-integration OAuth apps | App registration in each SaaS (admin-self-serve in most) |
| Auth among ourselves | GitHub team membership | Existing GitHub admin |
| Secrets | GitHub Actions org/repo secrets | Existing GitHub admin |
| AI provider | Anthropic API on a shared service identity | API contract already in place for Claude Code seats |
| Observability | GH Actions run history + Anthropic console + `#pm-toolkit-ops` Slack channel each agent heartbeats to | Existing Slack |
| Data envelope | Per-tool sponsor declaration, recorded at intake (see [Data classification](#data-classification-at-tier-a) below) | The tool sponsor |

What Tier A explicitly does **not** need:

- A k8s/serverless platform.
- A standard CD pipeline beyond `actions/deploy`.
- A shared Postgres / queue / object store.
- An egress proxy.
- An org SSO integration in front of a custom web surface.
- A centralized log aggregation backend with paging.

These are all real and useful — they're Tier B.

## Tier B — Engineering Co-Invested

A prioritized menu, each line tied to a concrete unlock on the roadmap:

| Eng investment | What it unlocks | Cost class |
|---|---|---|
| Shared egress proxy + allowlist | Tools that need to talk to non-SaaS endpoints | Small |
| Shared secrets vault beyond GH Secrets | API keys with rotation SLAs; service-to-service tokens with audit | Medium |
| Org SSO in front of a custom web surface | Tools used by non-PM internal audiences who don't have GitHub seats | Medium |
| Shared Postgres + schema management | Tools needing persistent state we own (cross-feature memory, audit history) | Medium |
| Centralized log aggregation + paging | SLO-grade reliability and a real on-call rotation for batch agents | Large |
| Shared k8s/serverless deploy surface | Long-running services beyond what GH Actions can host | Large |

If engineering funds none, we still ship at Tier A. If they fund some, specific items improve. The conversation with engineering becomes a procurement-style menu, not an unbounded ask.

## Architecture at Tier A

### The repo layout

One primary repo (`pm-toolkit`) plus a small number of agent repos, all under `rcg-digital-product`:

```
pm-toolkit/
  .claude/
    skills/
    commands/
  CLAUDE.md
  README.md

agents/release-notes/
  .github/workflows/run.yml      # cron + epic-close webhook
  agent.md                        # what it does, data classes, owner
  src/                            # API calls only; no custom infra
  README.md

agents/weekly-status/
agents/launch-checklist/
agents/living-spec-sync/
agents/proactive-sprint/
```

Each agent repo is:

- A workflow that triggers on cron or webhook.
- A short program (Python / Node) that calls the Anthropic API and writes the result into the appropriate SaaS via OAuth.
- A README declaring its data classes, its SaaS write surfaces, and the owning champion.

No deploy step. No infra to manage. Failures show up as failed workflow runs and a missing heartbeat in `#pm-toolkit-ops`.

### The handoff between interactive and batch

Champions use `pm-toolkit` to produce artifacts. Batch agents in the `agents/*` repos consume the same Jira / Confluence spine those artifacts created. The spine — Confluence PRD plus Jira epic — carries state across the lifecycle, exactly as [ROADMAP.md](../ROADMAP.md) already specifies. Tier A invents no state of its own.

### Where Tier A genuinely loses

1. **Observability is shallow.** If a batch agent silently produces wrong output for a week, we may notice from a consumer complaint, not a monitor. Mitigation: each agent posts a structured heartbeat (run id, item counts, error count) to `#pm-toolkit-ops` on every run. Missing heartbeats get noticed by humans, not paged.
2. **No defense-in-depth on egress.** A champion or an agent can call anywhere on the internet within the OAuth scopes they hold. Tier A relies on the SaaS-only architecture: there is nowhere else useful to call.
3. **Secrets are GitHub-scoped.** Rotation is manual; audit is GitHub's audit log, not a vault audit. Adequate for the scopes Tier A operates in (PM-write surfaces in Jira / Confluence / Slack); not adequate for anything with customer-data access.
4. **No real on-call.** A failing agent gets fixed in business hours by its owning champion. There is no pager. Tier A explicitly does not target SLOs.

These are not bugs in Tier A — they are the shape of the trade. Tier A items have to fit inside this shape or they don't belong in Tier A.

## Data classification at Tier A

Replacing a blanket data rule with a per-tool sponsor declaration. At intake, the tool sponsor records in the repo README:

- The highest data class the tool reads.
- The highest data class the tool writes.
- The SaaS systems it touches (read and write).
- The model provider and the routing (direct API on service identity vs. champion's Claude Code seat).

The declaration is the tier decision input. If the declaration fits inside Tier A's substrate — no customer PII, no regulated data, no production database access, only sanctioned SaaS — the tool runs at Tier A. If it crosses any of those, the tool either narrows its scope to fit, or it requests Tier B and stays in proposal status until the corresponding engineering investment lands.

The sponsor declaration is reviewed by product leadership at intake and by the tool's peer reviewer on every PR that changes data flow. The [security & data envelope](../governance/security-data-envelope.md) is the rule; the declaration is the operational artifact that demonstrates compliance with it.

## Tier mapping for current roadmap items

| Item | Tier | Note |
|---|---|---|
| PRD drafting assistant | A | Skill in `pm-toolkit`. |
| Story & ticket writer | A | Skill in `pm-toolkit`, writes via Jira API. |
| Release notes generator | A | Agent repo on GH Actions, epic-close webhook. |
| Weekly status synthesizer | A | Agent repo on GH Actions, weekly cron. |
| Backlog grooming copilot | A | Skill in `pm-toolkit`. |
| Stakeholder comms tailoring | A | Skill in `pm-toolkit`. |
| Spec → sprint decomposer | A | Skill in `pm-toolkit`. |
| Meeting → artifact pipeline | A | Skill in `pm-toolkit`, paired with existing meeting transcription SaaS. |
| PM knowledge agent | B | RAG with persistent index; needs Tier B storage and an SSO'd surface for non-licensed PMs. |
| Living spec sync | A | Agent repo on GH Actions, PR webhook. |
| Cross-functional launch checklist | A | Agent repo on GH Actions. |
| Proactive sprint agent | A | Agent repo on GH Actions, daily cron + heartbeats. |

Only the PM knowledge agent is Tier B at this read. That single item is the strongest case to make to engineering when asking for any Tier B investment at all.

## What changes in the existing docs

- **`governance/engineering-guardrails.md`** — re-sectioned: every guardrail now appears under its Tier (A or B). Most of the prior text was Tier B aspirational; this version makes that honest.
- **`governance/approval-model.md`** — no change. T0/T1 are the daily mode at Tier A. T2/T3 still apply and fire when a tool requests a Tier B upgrade.
- **`governance/ownership-model.md`** — no change. The model locates the gate at "owner of the end user," independent of platform.
- **`governance/security-data-envelope.md`** — no structural change. The per-tool sponsor data declaration becomes the operational artifact for compliance with the existing classification table.
- **`governance/github-org-structure.md`** — no change. The Tier A architecture meets the threshold for `rcg-digital-product` immediately (`pm-toolkit` plus 2–3 agent repos).
- **`ROADMAP.md`** — each item annotated `Tier A` / `Tier B`. Horizons unchanged.

## Triggers for upgrading a tool from Tier A to Tier B

- The sponsor's data declaration changes to include a class Tier A doesn't accept.
- The on-call burden on a tool's owning champion exceeds X hours/month for two months running.
- The tool needs a user surface outside the SaaS systems Tier A integrates with (e.g. a custom web UI for non-PM internal users).
- The tool's batch agent needs to call a non-SaaS endpoint that isn't reachable by default from GH Actions runners.

Each trigger is a PR against the tool's README that flips its tier, with a written justification. The flip is paired with a request to engineering for the specific Tier B capability it depends on. If engineering can't fund it, the tool's roadmap status moves to deferred.

## Risks and the case to leadership

- **We will build habits that don't use engineering's eventual platform.** When engineering does resource, we will have working Tier A patterns we'll be reluctant to migrate. Mitigation: every Tier B adoption is a deliberate per-tool migration, not a fleet-wide rebuild. We accept the duplication cost as the price of unblocking ourselves now.
- **We will under-monitor things that matter.** Tier A's "humans notice missed heartbeats" is real and adequate at low fleet size; it breaks at scale. Mitigation: fleet-size review at 5 agent repos asks whether Tier B observability is now needed.
- **We will appear to be working around engineering.** That is the perception cost. The substantive answer is that we are absorbing the failure cost of our own tools by accepting Tier A's narrower scope. We are not asking engineering to clean up after us; we are scoping ourselves to what we can clean up ourselves.
- **A Tier A tool's blast radius grows past its tier.** Per the [ownership model](../governance/ownership-model.md), reclassification is the answer. Tier A is not a permanent home for any specific tool; it is the substrate a tool lives on until its scope requires more.

## Open questions

- **Does Tier A handle prompt-injection-bearing inputs (Slack threads, ticket bodies) acceptably?** The envelope's prompt-injection rule already requires either human-in-the-loop review or a constrained tool surface. Tier A defaults to the human-in-the-loop side for everything interactive. Batch agents need explicit per-agent answers in their README.
- **How does Tier A degrade if Anthropic's API has an extended outage?** No fallback today. Acknowledge it; don't pretend otherwise.
- **GitHub Actions limits.** Minute caps, secret quotas, and concurrency limits become real at fleet size. Track them; flip to Tier B when they bind.

## Next steps

If this position holds:

1. Promote the Tier A / Tier B framing in `ROADMAP.md` and annotate each item. *(Included in the same change set as this doc.)*
2. Re-section `governance/engineering-guardrails.md`. *(Included in the same change set.)*
3. Stand up `rcg-digital-product` with the GitHub defaults Tier A requires (secret scanning, dependency scanning, branch protection). Do not block on engineering platform settings beyond those.
4. Create the first agent repo (release notes generator) end-to-end on Tier A as the proof point.
5. Pair this strategy doc with the direct memo at [`comms/2026-05-27-engineering-resourcing-product-leadership.md`](../comms/2026-05-27-engineering-resourcing-product-leadership.md) for the conversation with product leadership.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft. Reframes engineering's platform investment as a tier, not a precondition. |
