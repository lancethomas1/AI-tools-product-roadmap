# Devon — Platform PO

- **Persona type:** User (PO)
- **Tenure:** Mid-senior, 4–6 years as a PO (prior 3 years as a software engineer)
- **Team:** Platform / internal infrastructure
- **Lifecycle stages where active:** 2 (Planning), 3 (Execution), 5 (Post-release)
- **Owner:** Lance
- **Status:** Draft

## Principle

**Devon doesn't need help writing. He needs help knowing what is quietly rotting.** His artifacts are smaller and fewer than a growth PM's, but his dependency graph is larger and his backlog is older. The tools earn their place by surfacing what he can't see, not by drafting what he can already write in his sleep.

## Snapshot

- Owns the platform team: 8 engineers, no designer, 1 tech writer split with another team.
- Runs 3–4 active epics at a time, each with multiple consumer-team dependencies.
- Was a software engineer for 3 years before pivoting to PO. Reads PRs by default. Writes Postgres queries to understand his own backlog.
- Reports to a Director of Engineering, not Product. Sits in eng leadership meetings, not product council.
- Authors fewer PRDs (~1/quarter for major platform shifts) and far more tickets, ADRs, and migration plans.
- Stakeholder universe is mostly internal: consumer-team PMs, eng leads, security, SRE. Almost never writes for sales or customers.

## Day in the life

Wednesday, 10:00am. Devon is in a cross-team dependency sync. Three consumer teams are blocked on a platform migration his team committed to "end of Q3." His backlog has 217 open tickets, of which he believes ~40 are stale. One of those stale tickets is the blocker the consumer team is asking about — they filed it 4 months ago, it was triaged P3, and no one has looked at it since.

He spends the meeting scrolling Linear, finds the ticket, can't immediately remember why it was P3, has to dig through the ADR that justified the migration sequencing, and finally answers "we'll re-prioritize, give me 24 hours." After the meeting he opens the ticket, finds two near-duplicates filed by other consumer teams that he didn't know existed, and merges them. Total time spent on what should have been a 60-second answer: 35 minutes.

The pattern is "Devon's backlog is the source of truth that nobody — including Devon — fully trusts." Every cross-team conversation starts with backlog archaeology before it gets to the actual question.

## Motivations

| Motivation | What it looks like |
|---|---|
| **No surprises at standup** | The blocker someone raises on Monday is one Devon already knew about and had a plan for. |
| **Backlog you can actually navigate** | Search returns the one real ticket, not three near-duplicates and a stale ghost. |
| **Migration plans that survive contact** | Sprint decomposition matches what eng actually does; if it drifts, he learns within days, not the demo. |
| **Cross-team trust** | Consumer teams stop building local workarounds because Devon's platform delivers on its commitments. |

## Frustrations / anti-patterns

| Frustration | What it looks like | How a tool wins (or loses) him |
|---|---|---|
| AI that drafts when the problem is curation | Generates more tickets when his real problem is too many. | **Win:** auditor surfaces dupes/stale items with evidence. **Lose:** "I noticed you have many tickets, here is another one." |
| False-positive dupes | Two tickets with similar wording but different intent flagged as merge candidates. | **Win:** diff view at suggestion time, per-team learning from his dismissals. **Lose:** he merges two by mistake once and never trusts again. |
| Hidden cross-team drift | A consumer team is using the platform in a way the PRD never described. | **Win:** drift detector spots the implementation/PRD gap and surfaces it during planning, not after launch. **Lose:** drift only shows up in a post-mortem. |
| "Helpful" auto-merge / auto-close | Tool takes destructive action on its own. | **Win:** every action is a suggestion until Devon clicks. **Lose:** any auto-write. He files an incident the same day. |
| Generic AC | Tickets that don't mention the migration epic, the consumer team, or the rollback plan. | **Win:** tickets cite the spine, the dependency graph, and the rollback step. **Lose:** "As a developer, I want…" boilerplate. |
| Stale tools | The agent's index hasn't refreshed since last Friday, gives him 4-day-old answers. | **Win:** freshness shown alongside every output; refuses if too stale. **Lose:** confident-and-stale. |

## Jobs-to-be-done

| Stage | Job | Where in the day |
|---|---|---|
| 2. Planning | "Take this PRD and propose a sprint-ready ticket breakdown with dependencies, so eng can review and refine in one meeting instead of three." | Quarterly planning, occasional re-plans. |
| 3. Execution | "Tell me which tickets are stale, duplicate, or quietly drifting in priority. Show evidence." | Monday morning grooming pass; before any cross-team sync. |
| 3. Execution | "Surface anything about to go off the rails before I'm in standup hearing about it." | Tuesday/Thursday standups. |
| 5. Post-release | "Tell me where the implementation diverged from what we said we'd ship, so I can decide whether to update the PRD or fix the implementation." | Quarterly retro, post-launch reviews. |
| 5. Post-release | "Answer 'why did we decide X?' from the decision log without me re-reading three quarters of ADRs." | Cross-team meetings, planning. |

## Roadmap tool fit

| Tool (from ROADMAP) | Role for Devon | Why |
|---|---|---|
| **Backlog grooming copilot** (Next) | Primary | The single most-needed tool for his workflow. Backlog hygiene is his weekly bottleneck. |
| **Spec → sprint decomposer** (Next) | Primary | Saves him days at quarterly planning. |
| **Living spec sync** (Later) | Primary | Drift detection is exactly the post-launch signal he needs. |
| **Proactive sprint agent** (Later) | Primary | His standups should be reviewing the agent's findings, not discovering them. |
| **PM knowledge agent** (Later) | Primary | He's the persona who actually uses the "why did we decide" query repeatedly. |
| **Cross-functional launch checklist** (Later) | Primary | Platform launches have heavy cross-team dependency tracking. |
| **Story & ticket writer** (Now) | Secondary | He writes most tickets himself, but uses it for migration-plan bulk runs. |
| **PRD drafting assistant** (Now) | Secondary | ~1 PRD/quarter; useful but low frequency. |
| **Release notes generator** (Now) | Rarely | Platform changes ship as internal changelogs, not customer release notes. |
| **Stakeholder comms tailoring** (Next) | Rarely | Audience is mostly internal-eng; he tailors lightly by hand. |

## Tools he lives in today

Linear (heaviest user on the team), GitHub (PR-by-PR), Confluence (ADRs + migration docs), Slack (5 platform channels, 2 SRE channels), Datadog (read-only), Notion (sparingly).

## Success looks like (for Devon)

| Metric | Definition |
|---|---|
| Stale tickets in his backlog (rolling 30 days) | Drops from ~40 to <10. |
| Cross-team meeting time spent on backlog archaeology | Drops from ~30% to <5%. |
| Drift discovered post-launch | Drops from "most launches have surprises" to "most launches don't." |
| Time from "I need a sprint plan for this PRD" to a draft eng can refine | Drops from ~3 days (between meetings) to <1 day. |
| Tickets that pass eng DoR review on first refinement | Rises from ~50% to >85%. |

## Failure modes (how our tools can lose Devon)

| Failure | What he does about it |
|---|---|
| Auditor flags a high-confidence duplicate that isn't. He almost merges. | He stops trusting any merge suggestion above a certain confidence. Wide trust hit. |
| Drift detector reports drift on every PR because the PRD was deliberately vague. | He turns the surface off for that epic. |
| Sprint decomposer ignores a known dependency the consumer team flagged. | He goes back to manual decomposition, doesn't tell anyone the tool failed. |
| Knowledge agent gives a confident-wrong answer about a decision someone is litigating. | The decision gets relitigated in public; trust hit goes beyond him. |
| Proactive sprint agent is noisy; he gets 12 "you might want to look at…" pings per day. | He mutes the channel. Tool is functionally dead to him. |

## Quotes (illustrative, not real)

> "I don't need help writing. I need help knowing what's quietly rotting in the backlog while I'm in a meeting about something else."

> "If the tool auto-merges one ticket without my finger on the trigger, that's a same-day incident. I don't care how confident it is."

> "Half my week is archaeology. If you give me back that half, I will use this tool every day."

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft of Devon — Platform PO archetype. |
