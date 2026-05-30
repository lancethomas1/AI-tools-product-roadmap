# Sam — Staff Multi-Team PM

- **Persona type:** User (PM)
- **Tenure:** Staff, 8+ years as a PM
- **Team:** Multiple squads under one product area
- **Lifecycle stages where active:** 3 (Execution), 4 (Release), 5 (Post-release)
- **Owner:** Lance
- **Status:** Draft

## Principle

**Sam's bottleneck is context-switching, not authoring.** She runs two or three epics across different squads, and the same question gets asked of her by different audiences three times a week. The tools earn their place by making the org's memory queryable and by tailoring one source artifact for many audiences without her re-drafting it three ways.

## Snapshot

- Staff PM, 10 years in product (4 here, 6 elsewhere). Reports to a VP of Product.
- Runs 2–3 epics simultaneously across two squads (12 engineers, 2 designers between them).
- Owns the product area's roadmap, OKR alignment, and cross-team coordination. Less day-to-day execution, more steering.
- Heavy outbound communicator: weekly exec briefing, monthly sales-and-CS office hours, quarterly board readout slice, on-demand answers in 6 different Slack channels.
- Lives in meetings: 25–30 per week. Has a Loom-and-doc culture she's been pushing for two years to reduce that.
- The institutional memory for two prior product-area decisions nobody else remembers. She is asked "why did we decide X?" about once a week, on average.

## Day in the life

Thursday, 2:00pm. Sam just finished her third meeting of the day. In the meeting, the GM asked her "what's happening with the personalization epic?" — an epic she is not the direct PM on, but is the area lead for. She answered from memory. She's not 100% sure she had the latest numbers; the team's PM updated the ticket on Tuesday and she hasn't reviewed since.

At 2:15pm a Slack DM from a Sales AE: "customer is asking when X ships, what should I tell them?" Sam knows the answer is "Q3" but she also knows the team had a slip last week. She pings the team PM to confirm, then drafts a one-paragraph response for the AE.

At 2:30pm a retro question from another squad: "didn't we try this approach in 2025?" Sam thinks yes, knows there was a postmortem somewhere, can't immediately find it. She types a half-remembered answer into the retro doc with the caveat "I'd want to double-check."

At 3:00pm she opens her own draft of the monthly stakeholder update, which needs to ship Friday. It's a blank doc.

The pattern is **Sam is the routing layer between epics and audiences, and most of her day is answering 'what did we say' and 'what did we decide' questions for which the answer exists in writing but isn't where the asker can find it.**

## Motivations

| Motivation | What it looks like |
|---|---|
| **Org memory that works** | "Why did we decide X?" returns a citation, not a half-remembered answer. |
| **One source, many audiences** | She writes one update, the tooling produces the exec / Sales / CS / eng variants she would have hand-tailored. |
| **Cross-epic visibility** | She knows which epic is at risk before the GM asks. |
| **Less meeting-to-meeting whiplash** | Async artifacts replace 5 of her recurring meetings. |

## Frustrations / anti-patterns

| Frustration | What it looks like | How a tool wins (or loses) her |
|---|---|---|
| Re-litigating settled decisions | A new hire asks why we shipped X, no one remembers, the team almost re-decides it. | **Win:** knowledge agent surfaces the original decision with citation. **Lose:** knowledge agent gives a confident-wrong answer; the relitigation runs anyway, harder to settle. |
| Hand-tailoring one update three ways | Exec/Sales/CS variants take her an hour every Friday. | **Win:** Audience Tailor does it from her one approved source. **Lose:** any variant strips a real risk and exec misses it. |
| Stale-source confusion | Update cites a number the team's PM updated yesterday. | **Win:** synthesizer reads current state, shows source freshness. **Lose:** stale snapshot, embarrassing in front of GM. |
| Question-answer loops at scale | She answers the same "when does X ship?" four times a week. | **Win:** knowledge agent + status synthesizer mean Sales/CS can self-serve. **Lose:** they don't trust it, still ping her. |
| Cross-team risk surfacing too late | The personalization epic was at risk for two weeks before she heard about it. | **Win:** proactive sprint agent + drift detector flag mismatches early. **Lose:** noise so loud she ignores it, real risk buried. |

## Jobs-to-be-done

| Stage | Job | Where in the day |
|---|---|---|
| 3. Execution | "Tell me which of my epics is at risk this week and why, before anyone asks." | Monday morning. |
| 3. Execution | "Synthesize this meeting transcript into action items with owners and ticket drafts." | After every cross-team sync. |
| 4. Release | "Take this one update and give me the exec, Sales, CS, and eng variants." | Friday afternoon. |
| 5. Post-release | "Find the decision/postmortem/PRD that answers this question, with citations." | Multiple times a day, on demand. |
| 5. Post-release | "Flag where what we shipped diverges from what the PRDs said." | Before quarterly retro. |

## Roadmap tool fit

| Tool (from ROADMAP) | Role for Sam | Why |
|---|---|---|
| **PM knowledge agent** (Later) | Primary | Highest-leverage tool for her workflow. The org-memory query is her daily pain. |
| **Stakeholder comms tailoring** (Next) | Primary | She is the canonical user of the multi-audience tailor. |
| **Weekly status synthesizer** (Now) | Primary | Pulls across multiple epics; she can't keep up otherwise. |
| **Meeting → artifact pipeline** (Next) | Primary | She's in 25+ meetings/week. Auto-extraction with attribution is a step-change. |
| **Living spec sync** (Later) | Primary | Cross-epic drift detection is more valuable to her than to single-squad PMs. |
| **Proactive sprint agent** (Later) | Secondary | Useful, but she relies on team PMs to flag the day-to-day; she wants weekly cross-epic signal. |
| **Backlog grooming copilot** (Next) | Secondary | Her squad PMs use it more directly; she sees the rollup. |
| **Handoff runbook generator** (Later) | Secondary (as producer) | When a multi-team epic transfers to RTB, she (or her squad PMs) produce the runbook. For cross-team features the operational architecture and escalation sections are the hardest to assemble by hand. Jordan ([RTB PO](./jordan-rtb-po.md)) is the receiver. ([spec](../specs/handoff-runbook-generator.md)) |
| **PRD drafting assistant** (Now) | Secondary | She drafts ~2 PRDs per quarter, usually higher-level area docs. |
| **Story & ticket writer** (Now) | Rarely | Squad PMs own the ticket layer. |
| **Release notes generator** (Now) | Rarely | Squad PMs author per-epic notes; she rolls them up. |

## Tools she lives in today

Linear (rollup views), Confluence, Slack (12+ channels she actively reads), Notion (decision log she maintains personally because the company doesn't have a real one), Loom, Google Docs (for shared cross-functional planning), Calendar.app (effectively her primary work surface).

## Success looks like (for Sam)

| Metric | Definition |
|---|---|
| "Why did we decide X?" answered with a citation in <2 minutes | Rises from ~30% of asks to >80%. |
| Time spent per week on multi-audience tailoring | Drops from ~3 hours to <1. |
| Cross-epic risks surfaced before they reach the GM | Rises from "sometimes" to "default." |
| Meetings per week | Drops by 4–5 as async artifacts replace recurring syncs. |
| Decision relitigation incidents per quarter | Drops noticeably. |

## Failure modes (how our tools can lose Sam)

| Failure | What she does about it |
|---|---|
| Knowledge agent answers a contested decision confidently with one source, missing the dissent. | A reopened debate goes worse than it would have without the tool. Sam stops trusting the agent for anything political. |
| Audience Tailor strips a real risk from the exec variant. | She catches it once. The next exec variant she rewrites by hand, defeating the tool. |
| Status synthesizer rolls up across her epics but cites stale ticket numbers. | She sends an update with a wrong figure. GM notices. Reverts to manual. |
| Meeting → artifact pipeline misattributes a decision to the wrong owner. | Cross-team friction. Sam owns the apology. Tool gets disabled for that team. |
| Drift detector overloads her with `info`-severity findings across two squads. | She filters everything below `block`. Real `warn` signal lost. |

## Quotes (illustrative, not real)

> "I have three teams running and someone in retro just asked a question I already answered in a doc that nobody remembers."

> "I'd happily write one update a week if I didn't have to also write the exec version, the Sales version, the CS version, and the eng version."

> "If the knowledge agent gets one high-stakes question confidently wrong, I'll never use it for a high-stakes question again. Which means I'll never use it for the ones it'd help with most."

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft of Sam — Staff Multi-Team PM archetype. |
| 0.2 | 2026-05-30 | Lance | Added the handoff runbook generator to the tool-fit table as a producer-side tool (she hands multi-team epics off to RTB). |
