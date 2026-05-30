# Jordan — RTB PO

- **Persona type:** User (PO)
- **Tenure:** Mid-senior, 5–7 years as a PO (prior background in support engineering and ops)
- **Team:** Run-The-Business (BAU, defects, small enhancements, incident follow-ups)
- **Lifecycle stages where active:** 2 (Planning), 3 (Execution), 5 (Post-release)
- **Owner:** Lance
- **Status:** Draft

## Principle

**Jordan doesn't write new features; he keeps the existing ones honest.** His artifacts are smaller and fewer than a feature PM's, but his backlog is larger, older, and noisier, and his stakeholders ping him in real time. The tools earn their place by surfacing what is quietly rotting and by killing duplicate noise, not by drafting more of it.

## Snapshot

- Owns the Run-The-Business backlog: defect triage, customer-reported bugs, small enhancements, incident follow-ups, and operational/internal-tool tweaks across two adjacent product areas.
- Runs a rolling backlog of 250–350 open tickets at any time. New tickets land daily from Customer Success, Support, and incident reviews — not from a quarterly plan.
- 5 engineers + 0 designers; shares a tech writer with the broader product area. No fixed sprint cadence in practice — half the sprint is interrupt-driven.
- Came up through Support and Ops before pivoting to PO. Comfortable reading PRs and runbooks; doesn't write SQL but reads dashboards fluently.
- Reports to a Director of Product. Sits in the weekly CS/Support sync and the monthly incident review.
- Authors very few PRDs (~1–2 per year for larger BAU initiatives). Most of his writing is ticket bodies, prioritization rationale, and ad-hoc updates to CS/Support.
- Stakeholder universe is mostly internal: CS leads, Support managers, on-call engineers, account managers, occasionally compliance.

## Day in the life

Monday, 8:45am. Jordan opens Linear to a backlog of 312 open tickets. Over the weekend, on-call closed two incidents and filed four follow-up tickets. CS filed eleven new defect reports — at least two of which sound like the same recurring "exports time out on accounts >50k rows" bug he's seen before but can't immediately find. A Support manager has DM'd him asking when the "duplicate email on invite resend" fix is shipping; Jordan thinks the ticket was repriorized two weeks ago but isn't sure to what.

He spends the first hour of his day not triaging the new tickets but trying to figure out whether they're new. Two of the eleven CS reports turn out to be near-duplicates of an existing ticket from three weeks ago. One of the incident follow-ups is the third recurrence of a class of bug nobody has formally clustered yet. By 10am he has triaged 4 of 11 new tickets, written one Slack reply to CS, and not yet looked at his sprint board.

The pattern is **Jordan's backlog is a continuous arrival queue, and most of his time goes to deciding whether a new arrival is actually new.** Every new ticket starts with archaeology: have we seen this? Did we decide not to fix it? Is there an open one already? Who owns the answer?

## Motivations

| Motivation | What it looks like |
|---|---|
| **No surprises on a recurring defect** | The third report of "exports time out" comes in and Jordan already knew this was a known cluster with an owner and a plan. |
| **Backlog you can actually navigate** | Search returns the one real ticket, not three near-duplicates and a stale ghost from six months ago. |
| **SLA risk visible before it bites** | Tickets aging past their priority's SLA surface before CS opens a complaint. |
| **CS/Support self-serve** | CS can answer "when does X ship?" or "is this a known issue?" without DM'ing Jordan four times a day. |

## Frustrations / anti-patterns

| Frustration | What it looks like | How a tool wins (or loses) him |
|---|---|---|
| AI that drafts when the problem is curation | Generates more tickets when his real problem is too many. | **Win:** auditor surfaces dupes, stale items, and recurring-defect clusters with evidence. **Lose:** "I noticed you have many tickets, here is another one." |
| False-positive dupes | Two reports with similar wording but different root cause flagged as merge candidates. | **Win:** diff view at suggestion time, surfaces both repro steps side by side, per-team learning from his dismissals. **Lose:** he merges two by mistake once and never trusts the merge surface again. |
| Hidden recurring-defect classes | The same bug class has been filed five times under five different titles, none cross-linked. | **Win:** clustering surfaces "this looks like 4 prior tickets" with citations. **Lose:** clusters only emerge in a quarterly retro. |
| "Helpful" auto-close / auto-merge | Tool takes destructive action on its own. | **Win:** every action is a suggestion until Jordan clicks. **Lose:** any auto-write. He files an incident the same day. |
| Generic AC | Defect tickets that don't cite the CS report, the incident, or the customer impact. | **Win:** tickets cite the originating CS ticket, repro steps, affected customers, and (if relevant) the incident. **Lose:** "As a user, I want…" boilerplate on a bug. |
| Stale tools | The auditor's index hasn't refreshed since Friday, gives him answers that don't see the weekend's incidents. | **Win:** freshness shown alongside every output; refuses if too stale. **Lose:** confident-and-stale. |
| Noisy proactive agents | 14 "you might want to look at…" pings a day across two product areas. | **Win:** ranked, top-N, silent unless severity warrants. **Lose:** anything that out-pings #ops. |

## Jobs-to-be-done

| Stage | Job | Where in the day |
|---|---|---|
| 3. Execution | "Tell me which new tickets are duplicates of existing ones, with evidence. Show me the cluster, not just the pair." | Monday morning triage; after every CS sync. |
| 3. Execution | "Tell me which tickets are stale, quietly de-prioritized, or aging past their SLA. Show evidence." | Daily morning pass. |
| 3. Execution | "Surface anything about to breach SLA or recur as an incident before I'm in standup hearing about it." | Tuesday/Thursday standups; pre-incident-review. |
| 5. Post-release | "Tell me which defect classes are recurring, so I can decide whether to file a real fix epic instead of patching the next instance." | Monthly incident review; quarterly planning. |
| 5. Post-release | "Answer 'have we seen this before?' or 'why is this P3?' from the ticket and decision history without me re-reading three quarters of comments." | Multiple times a day, on demand. |
| 2. Planning | "Take this small initiative (the one PRD I write this quarter) and propose a ticket breakdown — most of my planning is not this, but when it is, I want it fast." | Quarterly, occasionally. |

## Roadmap tool fit

Three tools were specced specifically against this persona's arrival-queue, spine-less, SLA-driven workflow, because the general feature-PM tools assume a backlog that arrives on a plan and degrade exactly where Jordan's bottleneck lives. They lead the table as his primary tools.

| Tool (from ROADMAP) | Role for Jordan | Why |
|---|---|---|
| **Incoming defect triage copilot** (Next) | Primary (RTB-specific) | His #1 need. Per-arrival "is this new?" with a duplicate *cluster* and evidence, origin trace, SLA-clock start, and a draft triage — the half-his-morning archaeology, owned. Spine-less by design. ([spec](../specs/incoming-defect-triage-copilot.md)) |
| **SLA / aging sentinel** (Now) | Primary (RTB-specific) | "SLA risk visible before it bites." Read-only watch over aging tickets, ranked and capped, ahead of standup and CS syncs. The RTB analog of the sprint agent — clock is the SLA, not the sprint. ([spec](../specs/sla-aging-sentinel.md)) |
| **Known-issue responder** (Later) | Primary (RTB-specific) | Stops him being the human help desk. Lets CS/Support self-serve "is this known?" and "when does X ship?" with citations. ([spec](../specs/known-issue-responder.md)) |
| **Backlog grooming copilot** (Next) | Primary | The weekly maintenance sweep — staleness, drift, and the standing-backlog duplicate audit. Arrival-time triage now belongs to the triage copilot; grooming owns the periodic clean of what's already in. |
| **PM knowledge agent** (Later) | Primary | His own decision archaeology — "why is this P3?", "what did we try last time?". The CS-facing "is this a known issue?" slice is handled by the known-issue responder; this is the deeper, PM-side query surface. |
| **Meeting → artifact pipeline** (Next) | Primary | Incident reviews and CS syncs produce action items he is currently extracting by hand. |
| **Proactive sprint agent** (Later) | Rarely | Sprint-scoped, and Jordan has no fixed sprint. His version of "surface risk before standup" is the SLA / aging sentinel; the sprint agent itself is a poor fit. |
| **Story & ticket writer** (Now) | Secondary | He writes most defect tickets himself, but uses it for small-enhancement bulk runs and to enforce a consistent AC shape on CS-filed reports. |
| **Living spec sync** (Later) | Secondary | Useful where runbooks/specs drift from current behavior, but he has fewer PRDs to drift from than a feature PM. |
| **Weekly status synthesizer** (Now) | Secondary | He reports up on SLA, defect close rate, and incident follow-up; useful but not the bottleneck. |
| **Spec → sprint decomposer** (Next) | Rarely | He decomposes ~1 initiative a quarter; nice to have, low frequency. |
| **PRD drafting assistant** (Now) | Rarely | ~1–2 PRDs/year; very low frequency. |
| **Stakeholder comms tailoring** (Next) | Rarely | Audience is mostly internal CS/Support; he tailors lightly by hand. |
| **Release notes generator** (Now) | Rarely | RTB ships internal changelogs and CS-facing fix notes, not customer release notes. |
| **Cross-functional launch checklist** (Later) | Rarely | No feature launches; the closest analog is migration/deprecation, which is infrequent. |

## Tools he lives in today

Linear (heaviest user on his team), the CS ticketing tool (read-write), Slack (#incidents, #ops, #cs-product, plus 4 squad channels), PagerDuty (read-only), Confluence (runbooks + incident postmortems), GitHub (PR-by-PR review of fixes), the company's BI dashboard for SLA/defect-rate views.

## Success looks like (for Jordan)

| Metric | Definition |
|---|---|
| Duplicate-on-arrival rate (new tickets merged into existing within 24h) | Rises from ~20% to >60%. |
| Stale tickets in his backlog (rolling 30 days) | Drops from ~60 to <15. |
| Time per CS-filed ticket from arrival to triaged | Drops from ~15 minutes to <5. |
| Recurring-defect clusters identified before the 3rd recurrence | Rises from "rare" to "default." |
| Time spent per week on backlog archaeology | Drops from ~10 hours to <3. |
| Tickets that breach SLA without warning | Drops noticeably. |

## Failure modes (how our tools can lose Jordan)

| Failure | What he does about it |
|---|---|
| Auditor flags a high-confidence duplicate that isn't. He almost merges two unrelated defects. | He stops trusting any merge suggestion above a certain confidence. Wide trust hit. |
| Recurring-cluster surface lumps together two bugs that share keywords but not root cause. | He files the wrong fix epic; eng pushes back; he turns clustering off. |
| Proactive sprint agent is noisy — 14 pings a day across two product areas. | He mutes the channel. Tool is functionally dead to him. |
| Knowledge agent answers "have we seen this?" with a confident "no" when the answer was yes. | He files the same fix twice; CS notices the redundant ticket; trust hit goes beyond him. |
| Story writer produces polished defect tickets that don't cite the originating CS report. | Eng can't repro from the AC; refinement slows; he goes back to writing tickets by hand. |
| Tool's freshness lag means it didn't see the weekend's incidents and gives him a Monday triage view that's missing the new arrivals. | He stops trusting any Monday view from the tool. |

## Quotes (illustrative, not real)

> "I don't need help writing tickets. I need help knowing whether the ticket I'm about to write already exists three times over."

> "Half my morning is deciding whether a new bug is actually new. Give me that hour back and I'll use this tool every day."

> "If the auditor auto-merges one ticket without my finger on the trigger, it's a same-day incident. I don't care how confident the model is."

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-28 | Lance | Initial draft of Jordan — RTB PO archetype. Replaces the prior Platform PO (Devon) archetype to match the org's actual PO roles. |
| 0.2 | 2026-05-30 | Lance | Reworked "Roadmap tool fit": added three RTB-specific tools (incoming defect triage copilot, SLA / aging sentinel, known-issue responder) as primary, clarified the division of labor with the grooming copilot and PM knowledge agent, and downgraded the sprint-scoped proactive sprint agent (poor fit — no fixed sprint). |
