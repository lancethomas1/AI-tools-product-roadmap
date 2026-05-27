# Proactive sprint agent — Spec

- **Horizon:** Later
- **Stage:** 3 — Execution
- **Theme:** backlog-delivery
- **Owner:** TBD
- **Status:** Draft

## Why this tool, why now

Sprint plans go sideways quietly. A ticket that was supposed to land Tuesday slips to Thursday; another picks up a dependency nobody noticed; a customer-signal spike repriorities one item over another. Standup catches all of this — three days late. By then the PM is reactive: explaining to stakeholders why the sprint moved, re-cutting scope under pressure, and watching velocity erode.

This tool exists because **the data to predict these problems is already on disk**. Jira status changes, GitHub PR activity, Slack threads on blockers, customer-signal volume on linked tickets — all of it flows into the spine continuously. A tool that watches the sprint surface in real time and proposes interventions before standup converts "explain after the fact" into "decide before it gets worse."

We sequence it Later because:
- Trust-building. PMs need to trust the **suggestions** before they trust the **interventions**. The Backlog Grooming Copilot (Next) is the trust-building precursor on the same Backlog Auditor agent.
- Composability. The agent reads from grooming-cleaned backlogs, structured PRDs, and well-formed tickets — all of which mature in earlier horizons.
- Risk surfacing is also a Weekly Status Synthesizer (Now) feature. Once that signal is stable, we extend it from "weekly report" to "daily intervention proposal."

## What we mean by "proactive sprint agent"

This tool **watches sprint progress continuously** and proposes interventions: re-scope, re-prioritize, flag risk, escalate. It surfaces signals before standup so the PM walks in with proposed actions rather than discovering surprises.

**In our definition:**
- Daily sprint scan: ticket velocity, blocker age, customer-signal spikes, PRD scope drift
- Intervention proposals: re-scope (move ticket out), re-prioritize (swap order), flag risk (surface to PM + eng lead), escalate (notify stakeholders)
- Pre-standup digest: morning DM with proposed interventions
- Per-sprint progress narrative: "you're trending behind by 1.5 days," with cited evidence

**Not what this tool does:**
- Acting on interventions autonomously. Proposals only; PM commits.
- Sprint planning (that's Spec → Sprint Decomposer's job).
- Standup facilitation or note-taking (Meeting → Artifact Pipeline covers that).
- Cross-sprint or quarterly forecasting. Sprint-scoped only.
- Replacing the PM's judgment on what's actually a problem.

## Problem

Sprints fail in three predictable ways the PM only notices late:

1. **Silent slippage.** Ticket-3 was supposed to be in review Tuesday, but the PR is still open Thursday with no comment activity. Nobody flagged it. The PM finds out at Friday's standup.
2. **Dependency surprise.** Sprint planning slotted Ticket-A before Ticket-B; mid-sprint, eng realizes A actually depends on B, and the sprint re-shuffles under pressure.
3. **External shock unnoticed.** A customer-signal spike on a feature in the sprint changes its real priority, but the PM is heads-down and notices the support escalation a week later.

The tool's job is to make a **proposed, cited, intervention-shaped** signal the easy thing to act on — before it becomes a problem to explain.

## Users & jobs-to-be-done

**Primary:** PMs/POs running an active sprint.
**Secondary:** Eng leads receiving risk flags pre-standup.

1. *Tell me what's slipping* — tickets behind schedule with evidence.
2. *Propose what to do* — re-scope, re-prioritize, escalate.
3. *Catch the surprises* — customer-signal spikes, dependency emergences, PRD drift.
4. *Give me a morning brief* — pre-standup digest of proposals.

## In scope (v1)

- Daily scan over the active sprint (Backlog Auditor agent scoped to sprint).
- Slippage detection: tickets with no status change in N days, PRs open without review activity, in-progress assignees with no commits.
- Dependency-emergence detection: a ticket's PR references another open ticket via "depends on" or comment patterns.
- Customer-signal monitoring: per-ticket linked-signal volume vs. baseline.
- Intervention proposals per signal:
  - **Re-scope:** "move ticket X out of sprint" with rationale.
  - **Re-prioritize:** "swap A and B in order" with rationale.
  - **Flag risk:** notify PM + eng lead of an at-risk ticket.
  - **Escalate:** propose stakeholder notification for a high-confidence material slippage.
- Pre-standup digest: morning Slack DM to PM with the day's proposals.
- Per-sprint progress narrative: "trending [on/behind/ahead] by X days" with evidence.
- PM ack/dismiss on each proposal feeds eval set.

## Out of scope (v1)

- Auto-execution of interventions (auto-re-scope, auto-notify stakeholders).
- Cross-sprint forecasting.
- Sprint planning itself (Spec → Sprint Decomposer).
- Eng-lead-facing surface beyond risk-flag notifications.
- Quarterly portfolio dashboards.
- Auto-merging blocked tickets back to the next sprint without PM call.

## Capabilities

| Capability | Output | Trust gate |
|---|---|---|
| Slippage detection | Tickets with no recent activity + age | Pre-standup digest; PM reads and acts |
| Dependency-emergence detection | Newly-implied dependencies on in-sprint tickets | PM accepts as dependency or rejects |
| Customer-signal monitoring | Tickets with linked-signal spikes | Surfaced as risk flags |
| Re-scope proposal | "Move X out of sprint" + rationale + cited evidence | PM accepts / rejects / snoozes |
| Re-prioritize proposal | "Swap A and B" + rationale | PM accepts / rejects |
| Flag risk | Slack DM to PM + eng lead | Recipients acknowledge |
| Escalation proposal | Stakeholder-notification draft | PM reviews + sends |
| Per-sprint narrative | "Trending behind by 1.5 days" with evidence | PM reads in digest |

## Integrations

- **Backlog Auditor** ([agent](../governance/agent-library.md#4-backlog-auditor)) — sprint-scoped slice.
- **Spine Resolver** ([agent](../governance/agent-library.md#1-spine-resolver)) — epic + PRD.
- **Source Synthesizer** ([agent](../governance/agent-library.md#5-source-synthesizer)) — narrative compilation.
- **Citation Verifier** ([agent](../governance/agent-library.md#10-citation-verifier)).
- **PII Scrubber** ([agent](../governance/agent-library.md#9-pii-scrubber)).
- **Jira / Linear** — read sprint, tickets, status, comments.
- **GitHub** — read PR activity (open, reviews, comments, merges).
- **Slack** — output (digest + risk flags); read access scoped to PM's channels for blocker-context.
- **Customer signal** — Zendesk / Productboard linkages on tickets (read-only).

## UX surfaces

1. **Pre-standup Slack DM** — daily morning digest with intervention proposals.
2. **Plugin panel on sprint view** — at-a-glance signals on each at-risk ticket.
3. **Slack risk-flag DM** — high-confidence single-ticket alerts to PM + eng lead.
4. **Sprint dashboard widget** (Confluence sidebar) — trending narrative + open proposals.

No standalone app surface (operating principle 5).

## Trust & safety

- **No auto-execution.** Every intervention is a proposal; PM commits.
- **Citations on every proposal.** Re-scope rationale cites the PR / ticket / signal source.
- **Confidence thresholds.** Low-confidence signals filtered from digest; surface only above bar.
- **PM dismissal feedback.** Dismissed proposals feed per-team tuning so noise drops over time.
- **Eng-lead notifications are informational, not action-requests.** Risk flags go to eng lead as awareness; the PM owns the response.
- **No stakeholder escalation without PM review.** Even high-confidence escalations are drafts the PM sends.
- **PII Scrubber on ingress** (ticket bodies, customer-signal text).
- **Quiet hours.** No digests / notifications outside the PM's working hours.

## Success metrics

| Metric | Target |
|---|---|
| Sprint slippage caught pre-standup | >70% of cases retroactively rated as catchable |
| PM-rated proposal usefulness | >65% useful |
| Proposal acceptance rate | >40% |
| Dismiss-without-action rate | <20% |
| Sprint completion variance | -25% (sprints land closer to plan) |
| Surprise post-mortem mentions ("we didn't know X") | -60% |

## Rollout phasing

1. **Alpha (internal):** Slippage detection only. Daily digest, no risk flags. 1 team, 2 sprints.
2. **Beta:** Dependency-emergence + customer-signal monitoring. Risk-flag DMs live. 5 teams.
3. **GA:** Escalation proposals, sprint-dashboard widget, per-team confidence tuning, eng-lead notification opt-in.

## Dependencies & open questions

- **Depends on:** *Backlog grooming copilot* (Next). Backlog Auditor agent earns trust there before extending to sprint-scoped real-time.
- **Depends on:** *Story & ticket writer* (Now). Sprint signals require well-formed tickets with epic links and DoR-passing AC.
- **Depends on:** *Weekly status synthesizer* (Now). Risk-surfacing logic ports from weekly to daily; this is the same component evolved.
- **Depends on:** *PRD drafting assistant* (Now). PRD drift on in-flight items requires structured PRDs.
- **Depends on:** Backlog Auditor, Spine Resolver, Source Synthesizer, Citation Verifier, PII Scrubber.
- **Open:** Notification cadence. Daily morning digest + ad-hoc risk flags is the v1 default. Some teams may want digest-only; others may want every-major-event. Per-PM config.
- **Open:** Eng-lead opt-in scope. Eng leads are not always in the PM tool's audience. Should risk flags require eng-lead consent? Lean yes; opt-in at team config.
- **Open:** Calibration on "trending behind." Sprint velocity is noisy on small samples. The narrative needs caveats when n is small.
- **Open:** Re-scope proposal precision. A bad re-scope proposal ("move X out") that the PM acts on is more expensive than a missed slippage. The bar should be very high.
- **Risk:** Proposal noise erodes trust faster than weekly status because the cadence is daily. Mitigation: tight confidence threshold; dismissal feedback drives per-team tuning aggressively.
- **Risk:** PMs over-rely on the tool and stop reading raw signals. Mitigation: digest cites sources prominently; PM is expected to click through on at least the high-confidence items.
- **Risk:** Eng resentment ("the PM tool is telling us we're slipping"). Mitigation: framing — interventions are proposals to the PM, not to eng. Eng-lead notifications are awareness, not nag.

## Detection mechanics

### Slippage

1. Daily scan over sprint tickets.
2. Signals:
   - In-progress > N days with no status change.
   - In-progress with no commits or PR activity in N days.
   - PR open without review activity in M days.
   - Assignee with no Jira/GitHub activity at all in 3 days.
3. Per-signal confidence based on signal count + signal strength.
4. Above confidence threshold → surface in digest.

### Dependency emergence

1. PR descriptions, ticket comments, and Slack threads scanned for "depends on," "blocked by," "needs to wait for" patterns.
2. Matched targets reconciled against existing dependency graph; new dependencies surfaced.
3. PM accepts (graph updated) or rejects (false signal).

### Customer-signal monitoring

1. Per-ticket linked Zendesk / Productboard volume.
2. Compare current 7-day count vs. trailing 4-week mean.
3. Spike (>2x baseline) surfaced as risk.
4. Spike on a ticket descoped from sprint → re-prioritize proposal candidate.

### Intervention proposals

1. Signal aggregator combines slippage + dependency + customer-signal into a per-ticket score.
2. Re-scope proposal: "Move X out" fires when slippage is high, ticket is non-critical-path, and removing it lets the rest complete on time.
3. Re-prioritize proposal: fires when in-sprint ticket has lower current priority than a backlog item with rising customer signal.
4. Flag-risk: fires on aggregated score above threshold; sends to PM + eng lead.
5. Escalate: fires when material slippage on a milestone-anchored item is detected; PM reviews draft notification before sending.

### Per-sprint narrative

1. Source Synthesizer assembles per-day sprint state.
2. Trend computation: velocity-to-date vs. plan.
3. Narrative: "Trending behind by 1.5 days. Cited evidence: PR #123 stalled 4 days, ticket SCR-44 reopened twice."

## Evaluation criteria & metrics

### Layer 1 — Output quality

| Metric | Definition | Alpha | Beta | GA |
|---|---|---|---|---|
| Slippage precision | Slippage flags PM rates "real" | >0.70 | >0.80 | >0.90 |
| Slippage recall (vs. retroactive audit) | Real slippages caught | >0.65 | >0.75 | >0.85 |
| Dependency-emergence precision | Newly-flagged dependencies that PM/eng confirms | >0.65 | >0.75 | >0.85 |
| Customer-signal-spike precision | Spikes flagged that PM rates as worth reading | >0.55 | >0.65 | >0.80 |
| Re-scope proposal precision | Proposals PM accepts | >0.40 | >0.50 | >0.65 |
| Per-sprint narrative accuracy | Trend assertions that audit confirms | >0.85 | >0.92 | >0.97 |

**Datasets:** historical sprints with retrospectively-labeled slippage / dependency / spike events (n>30 sprints across 5 teams), refreshed quarterly. A 20-case adversarial set of "noisy but not problematic" sprint days the tool should NOT flag.

### Layer 2 — Product behavior

| Metric | Pass bar |
|---|---|
| Digest open rate | >75% week 1, >55% week 8 (fatigue check) |
| Proposal review rate | >70% of surfaced proposals get reviewed |
| Acceptance rate | >40% of reviewed proposals acted on |
| Dismiss-without-review rate | <20% (high = trust low) |
| Time-to-first-action from digest open | <2 minutes p50 |

### Layer 3 — Outcomes

| Metric | Target |
|---|---|
| Sprint completion variance | -25% |
| "Surprise" post-mortem mentions | -60% |
| Time spent re-cutting scope under pressure | -50% |
| Eng-lead-reported confidence in PM-team visibility | +15 points |

### Guardrails

| Guardrail | Limit |
|---|---|
| Auto-execution of any intervention | 0 (hard bar) |
| Notifications outside PM working hours | 0 (hard bar) |
| Stakeholder escalation without PM review | 0 (hard bar) |
| Per-PM daily proposal ceiling | 5 (above = noise) |
| PII regex matches in any digest | 0 |
| Cost per sprint per team | <$5 (GA) |

### Anti-metrics

- **Proposals per day.** Volume isn't value — the daily cadence makes noise more expensive.
- **Acceptance rate alone.** Maximizing it pushes toward safe high-confidence cases and misses the dependencies PMs needed help noticing.
- **Time-on-tool.** PMs want fewer surprises, not longer tool sessions.

## Failure modes & mitigations

| Failure | What it looks like | Mitigation |
|---|---|---|
| False slippage flag | Ticket flagged stalled but the work is happening off-system | Per-PM dismissal feedback; "off-system progress" status tag |
| Missed slippage | Real slippage no signal caught | Quarterly audit feeds eval; team-level threshold tuning |
| Bad re-scope proposal | Tool proposes moving the critical-path item out | Critical-path-aware proposals via DAG (consumes Spec → Sprint Decomposer output) |
| Noise digest | 10 proposals every morning, PM tunes out | Daily ceiling; confidence threshold per-PM tunable |
| Eng-lead resentment | Risk flags read as PM-side complaint | Opt-in eng-lead inclusion; framing emphasizes awareness |
| Stale customer-signal baseline | Baseline mean from a quiet period, every week looks like a spike | Rolling 4-week window updated continuously |
| Notification timing | Digest arrives at 7am to a PM whose day starts at 10 | Per-PM timezone + quiet-hour enforcement |
| Premature escalation | Material-slippage escalation draft sent before PM realized | Hard bar: PM clicks send; tool only drafts |
| PRD-drift overload | Living Spec Sync drift flags duplicated in sprint agent | De-dup with Living Spec Sync; sprint agent surfaces drifts only when sprint impact exists |

## Cost & latency envelope (rough)

- **Daily scan:** Backlog Auditor sprint-scoped + LLM rationale on top-K signals. ~$0.20 per team per day.
- **Real-time risk flags:** event-driven, small LLM calls. ~$0.02 per flag.
- **Per-sprint narrative:** Source Synthesizer. ~$0.05 per scan.
- **Per-team monthly cost:** ~$10–$20.
- **p95 latency:** digest in <2 minutes from scan-start; risk-flag DMs in <30 seconds from triggering event.

## User-flow walkthroughs

### Persona flow

```mermaid
sequenceDiagram
    participant Sources as Jira / GitHub / Slack / Signal
    participant Tool as Proactive Sprint Agent
    actor PM
    actor Eng as Eng Lead
    actor Stakeholder

    Note over Tool: Daily scan + event-driven
    Sources-->>Tool: Sprint state + activity + signal
    Tool->>Tool: Detect slippage / deps / spikes; rank proposals
    Tool->>PM: Pre-standup digest with proposals
    opt High-confidence risk
        Tool->>Eng: Awareness DM (opt-in)
    end
    PM->>PM: Ack / accept / dismiss proposals
    PM->>Sources: Re-scope, re-prioritize as accepted
    opt Material slippage
        Tool-->>PM: Drafted escalation note
        PM->>Stakeholder: Send escalation (PM commits)
    end
```

### Flow A — Morning digest catches a stall

7:30am Tuesday, PM gets the Slack DM: "*Sprint 24 brief: 2 proposals, 1 risk flag, trending on-track.*
- *Proposal:* Re-scope SCR-44 (CSV export). PR has been open 5 days with no review; eng has flagged it in Slack as 'waiting on backend.' Backend ticket isn't in this sprint. Suggested action: move SCR-44 to next sprint.
- *Proposal:* Re-prioritize: SCR-50 has 4 new Zendesk tickets in 3 days vs. baseline 0. Currently P3; suggest P2 swap with SCR-48.
- *Risk flag:* SCR-41 assignee has had no GitHub activity since Thursday; check in?"

PM ack's the SCR-41 risk (assignee is on PTO, no real risk), accepts the SCR-44 re-scope, defers the SCR-50 reprio to standup discussion. Standup at 9am is 4 minutes shorter because the moves were decided 90 minutes earlier.

### Flow B — Dependency emergence catch

Mid-sprint, PR for SCR-47 contains a comment: "Waiting on the new permission API endpoint from SCR-52." SCR-52 is in a different epic, not in this sprint. Tool surfaces a dependency-emergence proposal: "SCR-47 implicit dependency on SCR-52. Acknowledge? Reject?" PM acks, talks to the SCR-52 owner, gets a temporary stub agreed, sprint stays on plan.

### Flow C — Escalation draft, PM-sent

Late in the sprint, the Backlog Auditor scores SCR-30 at high-risk-of-slipping-out: 3 dependency hits, 2 PR-stall days, customer signal spike. Tool drafts an escalation: "Bulk-import feature appears at material risk of slipping past the planned ship date. Suggested stakeholder note: [draft]." PM reviews, agrees, edits the draft, sends to the stakeholder channel themselves. The narrative tomorrow morning reflects the escalation as sent.

## Anti-goals

- **Won't auto-execute interventions.** Proposals only.
- **Won't escalate without PM review.** Draft + PM send.
- **Won't nag eng directly.** Eng-lead awareness only; PM owns response.
- **Won't run without a sprint.** Sprint scope is the unit.
- **Won't cross-sprint forecast.** Sprint-bounded only.
- **Won't ignore the PM's working hours.** Quiet hours enforced.
- **Won't replace standup.** It informs; standup still happens.
