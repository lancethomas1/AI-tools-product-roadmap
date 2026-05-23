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
