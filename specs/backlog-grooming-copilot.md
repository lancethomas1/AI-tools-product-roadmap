# Backlog grooming copilot — Spec

- **Horizon:** Next
- **Theme:** backlog-delivery
- **Owner:** TBD
- **Status:** Draft

## Why this tool, why now

Unlike PRD drafting (kickoff-bound), story writing (refinement-bound), or release notes (ship-bound), backlog grooming is a continuous tax the backlog levies every week, on every active project, regardless of where individual features sit in their lifecycle. The cost compounds linearly with backlog size — and backlogs only grow. A copilot here has phase-independent ROI and produces the cleanest weekly signal on whether PMs will trust AI-generated judgment calls, which de-risks every later tool on the roadmap.

## Problem

Backlogs decay. Duplicates accumulate as different stakeholders file the same complaint in different words. Tickets go stale when the reporter loses interest or the world moves on. Priority drifts from stated goals because no one re-reads the backlog top-to-bottom. PMs spend 2–4 hours a week on grooming that is mostly pattern matching, not judgment — and they do it badly because the cognitive load of reading hundreds of tickets is real.

## Users & jobs-to-be-done

**Primary:** PMs/POs owning a Linear or Jira project with >100 active tickets.
**Secondary:** Eng leads who want to walk into grooming with a pre-cleaned list.

1. *Before grooming*, tell me what's redundant, stale, or mis-prioritized so I review judgments, not raw lists.
2. *When I file a new ticket*, warn me if it's a likely duplicate before it hits the backlog.
3. *Show me* which open items no longer link to any active goal or customer signal.

## In scope (v1)

- Duplicate detection across open tickets in the same project (semantic, not just title match), with merge suggestions.
- Stale detection: open items with no comment/update in N days AND no linked active goal.
- Priority drift: items whose stated priority disagrees with linked OKR/goal weight or customer-signal volume.
- Re-rank suggestions: ranked list of "items to look at" before each grooming session, ordered by confidence × impact.
- Pre-grooming digest synthesized into a markdown report the PM reviews before the meeting.
- File-time duplicate warning at ticket creation, surfaced in the Linear/Jira plugin.

## Out of scope (v1)

- Auto-closing, auto-merging, or auto-re-prioritizing without human approval.
- Cross-project deduplication — one project at a time keeps the trust bar tractable.
- Discovery / new-idea generation — this tool only operates on tickets already filed.
- Direct Slack or Zoom ingestion — use already-linked customer-signal sources only.

## Capabilities

| Capability | Output | Trust gate |
|---|---|---|
| Duplicate suggestion | Pairs of tickets with similarity score + 1-line rationale + cited lines | PM clicks merge / dismiss |
| Stale flag | Items with last-activity date and reason for staleness | PM clicks keep / close / snooze |
| Priority drift | Item + current priority + suggested priority + reason | PM accepts / edits / rejects |
| Pre-grooming digest | Markdown summary delivered day-before grooming | PM edits before sharing |
| File-time dup warning | Inline panel in ticket create flow | PM proceeds / merges into existing |

## Integrations

- **Linear** (primary) and **Jira** (parity by v1.1) — read tickets, comments, links, labels, priority, hierarchy.
- **Linked goals / OKRs** — read-only from whatever the team uses (Linear projects, Jira Advanced Roadmaps, Notion table). Configurable.
- **Customer signal** — Zendesk / Intercom / Productboard *linkages already present on tickets*. Do not crawl raw conversations.
- **Slack** — output only (digest delivery). No Slack ingest in v1.

## UX surfaces

1. **Plugin panel** in the Linear/Jira ticket sidebar — duplicate + drift suggestions on the open ticket.
2. **Pre-grooming digest** posted to a configurable Slack channel or DM the day before grooming.
3. **Bulk review view** — web view (auth via Linear/Jira) listing all open suggestions, batch-actionable.

No standalone app surface (per operating principle 5).

## Trust & safety

- Every suggestion ships with at least one cited source (linked ticket IDs, goal IDs, signal source).
- Confidence score visible on every suggestion; PM can set a project-wide threshold below which suggestions are hidden.
- Dismissals are sticky and feed back into per-project tuning.
- Audit log of every accepted action, queryable by PM.
- PII scrubbing on ticket bodies before any model call (operating principle 4).
- No writes without PM confirmation in v1. v2 may consider "auto-merge on >0.95 confidence" gated by per-project opt-in.

## Success metrics

| Metric | Target |
|---|---|
| Stale/duplicate tickets in backlog (rolling 30-day) | -40% (matches roadmap goal) |
| PM hours/week spent on grooming | -50% |
| Suggestion acceptance rate | >40% (lower = too noisy) |
| Dismiss-without-review rate | <15% (higher = low trust) |
| Active projects with the tool enabled | >70% within a quarter of GA |

## Rollout phasing

1. **Alpha (internal):** Read-only digest emailed to 3 friendly PMs. No plugin yet. Validates signal quality before we ship surface area.
2. **Beta:** Linear plugin with duplicate + stale flags. Jira parity not required. 10 PMs.
3. **GA:** Full capability set, Jira parity, bulk review view, configurable thresholds.

## Dependencies & open questions

- **Depends on** the *Story & ticket writer* (Now). Grooming signal is only as good as the structure of the tickets it reads; if the writer ships first, grooming becomes meaningfully easier.
- **Open:** Does customer-signal weighting need per-team config, or can we infer it from existing linkage patterns?
- **Open:** Cold start — the first month with no dismissal feedback may be too noisy. Ship with a hand-tuned per-project-type threshold?
- **Open:** Data residency on customer-signal text — re-confirm with privacy review before reading Zendesk fields.
- **Risk:** Eng leads may read this as "PM offloading their job onto eng review." Frame the digest as PM-owned output, not eng-facing.

## Detection mechanics

### Duplicate detection
- Embed title + body + label set with a sentence-embedding model; cosine similarity over the project's open ticket set.
- Top-K candidates pass a re-rank step comparing requester overlap, label overlap, linked-customer overlap, and a short LLM rationale ("is B a duplicate of A?") returning a confidence scalar.
- Final score = 0.6 × embedding similarity + 0.25 × overlap features + 0.15 × LLM confidence.
- Surface suggestions ≥0.7; auto-hide <0.5 (configurable per project).

### Staleness scoring
`score = w1·days_since_last_comment + w2·no_active_goal + w3·no_recent_customer_signal + w4·priority_decay`

- Weights tuned per-project from accept/dismiss feedback.
- Hard floors: never flag items <30 days old or with an in-progress assignee.

### Priority drift
For each open ticket with priority P:
1. Compute *expected* priority from `linked_goal_weight × customer_signal_volume × age_decay`.
2. If `|P − expected| ≥ 1` priority band, emit a drift suggestion with the gap and contributing reasons.
3. Ignore items the PM has manually re-prioritized in the last 14 days — respect the human override.

## Evaluation criteria & metrics

We measure on three independent layers. Hitting one without the others is a known failure mode — a 95%-precision model that PMs never look at, or high acceptance on suggestions that don't actually shrink the backlog. All three must move.

### Layer 1 — Output quality

What the model produces, measured against ground truth, independent of whether a PM acts on it.

#### Duplicate detection

| Metric | Definition | Alpha | Beta | GA |
|---|---|---|---|---|
| Precision @ 0.7 threshold | True dups / suggested dups | >0.80 | >0.85 | >0.90 |
| Recall @ 0.7 threshold | Suggested true dups / all true dups in set | >0.50 | >0.60 | >0.70 |
| Brier score (calibration) | MSE of confidence vs. outcome | <0.15 | <0.12 | <0.10 |
| Adversarial pass rate | Paraphrased dup pairs the model must catch | >0.60 | >0.75 | >0.85 |

**Datasets:**
- **Hand-labeled golden set** — 200 ticket pairs per pilot project labeled by that project's PM. Refreshed quarterly.
- **Historical ground truth** — items the PM merged or closed-as-duplicate in the past 6 months. Free and large, biased toward easy cases.
- **Adversarial set** — ~50 manually constructed near-duplicates (same root cause, different wording, different customer surface) to test ceiling.
- **Synthetic negatives** — pairs from different projects (must never match) injected at 10% rate to catch over-eager matching.

#### Staleness

| Metric | Definition | Pass bar |
|---|---|---|
| False-stale rate | Flagged stale items the PM marks "active off-ticket" | <10% |
| Catch rate | Stale items flagged before PM finds them in grooming | >70% of items PM marks stale |
| Median age at flag | Days from staleness onset to surface | <14 days |

Precision dominates over recall here — flagging an active conversation as stale erodes trust fast; missing one is recoverable next week.

#### Priority drift

| Metric | Definition | Pass bar |
|---|---|---|
| Expert agreement | Drift suggestions a senior PM endorses on a blind 50-item audit | >65% |
| Override-respect rate | Suggestions violating the 14-day human-override cooldown | 0% (hard bar) |
| Reason fidelity | Suggestions whose stated rationale checks out against linked goals/signals | >90% |

### Layer 2 — Product behavior

What PMs actually do with the output. Telemetry, not labels.

| Metric | What it tells us | Pass bar |
|---|---|---|
| Digest open rate | Are PMs even looking? | >80% week 1, >60% week 8 (fatigue check) |
| Suggestion review rate | Of digest items, how many get clicked into | >70% |
| Acceptance rate | Of reviewed suggestions, how many get acted on | >40% |
| Dismiss-no-review rate | Suggestions dismissed without opening detail | <15% |
| Time-to-action | Median seconds from digest open → first action | <45s |
| In-product rating | useful / noisy / wrong, weighted | >60% useful, <10% wrong |

**Suggestion fatigue is the metric I'm most worried about.** Track digest open rate as a rolling 4-week window per PM; two consecutive down-weeks triggers a "tune your thresholds" prompt before that PM churns off the tool entirely.

### Layer 3 — Backlog outcomes

Whether the backlog actually gets healthier. The headline numbers — and the hardest to attribute.

| Metric | Target | Measurement |
|---|---|---|
| Stale/duplicate count (rolling 30d) | -40% vs. pre-tool baseline | Monthly snapshot per project |
| PM hours/week on grooming | -50% | Quarterly time survey + Linear/Jira activity proxy |
| Median backlog age | -25% | Auto-computed from open-ticket timestamps |
| Tickets resolved per active engineer | Flat-to-up (sanity: we're not just deleting work) | Linear/Jira |
| Backlog-health NPS (PM-reported) | +15 points | Quarterly survey |

**Counterfactual.** Shadow-mode beta gives one comparison point. For GA, hold one project per pilot org out of the tool for a quarter as a within-org control. Not a clean RCT, but better than self-reports alone.

### Guardrail metrics

Hard limits. Exceeding any of these blocks the next phase, regardless of how headline metrics look.

| Guardrail | Limit | Why |
|---|---|---|
| Revert rate (merge then unmerge within 7d) | <5% | High revert = false confidence in dup suggestions |
| PII regex matches in model output | 0 | Privacy floor; fail-closed redaction must hold |
| Override-violation incidents | 0 | Tool must respect human overrides absolutely |
| P0: bad write needing manual rollback | 0 | v1 has no autonomous writes — any incident is a bug |
| Cost per PM per month | <$15 (GA) | Margin sanity |

### Anti-metrics

Explicitly *not* optimized for:

- **Total suggestions generated.** Volume isn't value. Fewer better suggestions, not a noisier feed.
- **Acceptance rate alone.** Maximizing acceptance pushes us toward only-confident cases, killing recall on the hard dups PMs actually need help with.
- **Time-on-tool.** PMs want grooming faster, not longer. Lower time-to-action is the goal.
- **Suggestions-per-digest growth.** This number should *decline* over time as the backlog gets healthier.

### Measurement cadence & ownership

| Layer | Cadence | Owner | Source of truth |
|---|---|---|---|
| Output quality | Per-release on eval set | ML / model owner | Labeled datasets + offline harness |
| Product behavior | Real-time dashboard, weekly review | PM on this tool | In-product telemetry |
| Backlog outcomes | Monthly snapshot, quarterly review | PM + pilot project owner | Linear/Jira API + survey |
| Guardrails | Continuous alerting | On-call | Telemetry + log scan |

## Failure modes & mitigations

| Failure | What it looks like | Mitigation |
|---|---|---|
| False-positive duplicate | Two distinct customer requests merged | Per-project confidence threshold; PM-only merge; "report bad suggestion" feeds eval set |
| Stale flag on active work | Item flagged because conversation lives in Slack, not the ticket | Stale check requires no-comment AND no-linked-goal; PM can mark "active off-ticket" |
| Drift contradicts PM judgment | Tool keeps re-surfacing a deliberate deprioritization | 14-day human-override cooldown on drift suggestions |
| Cascading bad digest | First digest is noisy, PM disengages | Alpha is read-only with explicit PM rating before plugin ships |
| PII leakage | Customer name/email reaches the model | Pre-call redaction with a tested allowlist; fail closed on regex match in output |
| Eng frustration | Eng reads digest as a complaint list | Frame as PM-to-PM; eng-facing summary deferred to v2 |

## Cost & latency envelope (rough)

Sizing target: project with ~500 open tickets, weekly full re-scan.

- **Embeddings:** ~500 vectors, incrementally refreshed → negligible after warm.
- **Re-rank LLM calls:** top-30 candidate pairs × 3 dimensions = ~90 short prompts. ~$0.05–$0.15 per scan.
- **Drift scoring:** deterministic, no model calls.
- **File-time dup warning:** 1 embedding + ≤5 re-rank prompts per ticket create. p95 latency target <800ms.
- **Per-PM monthly cost ceiling:** <$5 (alpha), <$15 (GA with file-time warnings live).

## User-flow walkthroughs

### Flow A — Wednesday pre-grooming digest

Tuesday 5pm UTC the scheduler runs the project scan. Wednesday 8am local the PM gets a Slack DM:

> *Backlog digest for Mobile-iOS — 6 dup candidates, 12 stale items, 4 priority drifts. [Open review →]*

PM clicks through to the bulk review view, batch-dismisses 3 dup candidates with a one-click "not duplicates because…", merges 2, snoozes 8 stale items, accepts 1 drift. Total time: ~7 minutes. Grooming meeting starts with a 30-item shortlist instead of a 312-item backlog.

### Flow B — File-time dup warning

A CS rep files *"Login broken on iPad Pro after 17.4 update"*. As they save, the sidebar shows:

> *Possible duplicate (87%): MOB-2841 "iPad Pro login fails post-iOS-17.4". Filed 3 days ago, 4 customer reports linked.*

CS rep clicks "Add my customer to MOB-2841" instead. The duplicate never enters the backlog.

### Flow C — Drift suggestion the PM rejects

Tool suggests promoting MOB-3120 from P3 → P2 because three new Zendesk tickets link to it. The PM knows the team is mid-sprint on a higher-impact item and dismisses with "tracking, will revisit next planning." The 14-day cooldown engages; MOB-3120 doesn't re-surface until cooldown elapses.

## Anti-goals

Explicit non-features, to prevent scope drift:

- **Won't write tickets for the PM.** That's the Story & ticket writer's job. Grooming reads, judges, proposes — it doesn't author.
- **Won't post to customers.** Nothing this tool produces is customer-facing.
- **Won't make priority calls autonomously.** Drift suggestions are inputs to PM judgment, never replacements.
- **Won't cross project boundaries in v1.** Cross-project dedup is a harder problem with a different trust bar.
- **Won't surface anything without a citation.** If we can't show the PM why, we don't show the suggestion.
