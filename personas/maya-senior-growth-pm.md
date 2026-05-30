# Maya — Senior Growth PM

- **Persona type:** User (PM)
- **Tenure:** Senior, 6–8 years as a PM
- **Team:** Growth squad, customer-facing
- **Lifecycle stages where active:** 1 (Drafting), 3 (Execution), 4 (Release)
- **Owner:** Lance
- **Status:** Draft

## Principle

**If a tool can't save Maya a meaningful chunk of a working day, she will close the tab and never reopen it.** Maya's bar for AI-generated content is high because her time pressure is high. The tools either compress her workweek or they don't exist to her.

## Snapshot

- Ships customer-facing features on a 2-week cadence, sometimes faster.
- Owns one squad of 6 engineers + 1 designer; runs 1–2 active epics at any time.
- Reports up to a Director of Product. Reports out to Sales, CS, and Exec on a weekly drumbeat.
- Has been at the company 3+ years. Knows the codebase well enough to read PRs.
- Is the squad's primary writer of: PRDs, release notes, weekly status, exec briefings, sales enablement bullets.
- Has tried 4 AI tools in the past year. Kept one (a meeting transcriber). Quietly abandoned the others.

## Day in the life

Tuesday, 9:15am. Maya has 35 minutes before standup, a half-written PRD for next sprint's experiment, a Slack thread asking when the *latest* shipped feature gets a release note, an exec ping asking "what's the headline from your team this week?", and three Linear notifications about stale tickets on the prior epic that nobody has groomed since launch.

She picks the exec ping first because it's the loudest. She drafts a three-sentence answer from memory, second-guesses one of the sentences, and goes hunting through Slack for the metric she's trying to cite. She finds the metric, edits the message, sends it. 11 minutes gone. Now she has 24 minutes for the PRD she actually came in to write.

The pattern repeats Wednesday. And Thursday. By Friday she's behind on the PRD she wanted to ship at start of week, and she writes the weekly status update at 5:45pm because nothing else has a deadline that hard.

The pattern is not "Maya is bad at her job." The pattern is that the **meta-work eats the execution time**, and every minute Maya spends drafting an exec ping is a minute she is not spending on the bet her squad is supposed to be making.

## Motivations

| Motivation | What it looks like |
|---|---|
| **Squad velocity** | Tickets crisp, PRD published before kickoff, status updates not a Friday-night fire drill. |
| **Exec trust** | When the Director asks "what's happening with X", Maya has a paragraph ready that doesn't require her to dig. |
| **Sales enablement** | Sales doesn't ping her with "what's the talking point on the new feature?" because the release note already gave them one. |
| **Time to think** | Two uninterrupted hours per week to actually do strategy work, not just dispatch artifacts. |

## Frustrations / anti-patterns

| Frustration | What it looks like | How a tool wins (or loses) her |
|---|---|---|
| AI output that needs heavy rewrite | She'd have been faster from scratch. | **Win:** drafts that read like her on her best day. **Lose:** marketing-voice slop she has to redact line by line. |
| Tool that lies confidently | A status update claims a ticket shipped that didn't. | **Win:** every claim cites a source she can click. **Lose:** plausible-sounding fabrication. One incident and she's done. |
| Yet another surface to learn | Standalone app, new login, new keyboard shortcuts. | **Win:** lives in Linear, Confluence, Slack. **Lose:** "open the AI tool" as a step. |
| Loud tools | Daily digests, notification storms, "I noticed…" pings. | **Win:** silent unless there's a real finding. **Lose:** anything that out-pings her squad. |
| Generic AC and templated stories | Tickets that read like every other AI-written ticket. | **Win:** trained on her squad's accepted tickets, picks up her tone. **Lose:** "As a user, I want to…" boilerplate every time. |
| Customer-facing leaks | Release note draft includes an internal-only metric. | **Win:** strict PII/internal-tag scrubbing on customer-bound paths. **Lose:** any near-miss. |

## Jobs-to-be-done

Mapped to lifecycle stages from [ROADMAP.md](../ROADMAP.md).

| Stage | Job | Where in the day |
|---|---|---|
| 1. Drafting | "Get me to a reviewable PRD from a one-paragraph brief in under 20 minutes." | Tuesday/Thursday mornings, before standup. |
| 3. Execution | "Give me a weekly status update I can send by 4pm Friday with one round of edits, citing what actually shipped." | Friday afternoon. |
| 4. Release | "Generate release notes, an internal changelog, and a Slack announcement from this epic's merged PRs." | Day-of release. |
| 4. Release | "Tailor this update for the Director, for Sales, and for CS — same facts, three audiences." | Following the status update. |

## Roadmap tool fit

| Tool (from ROADMAP) | Role for Maya | Why |
|---|---|---|
| **PRD drafting assistant** (Now) | Primary | First draft of weekly PRDs. Her bar is "reviewable in 20 minutes." |
| **Weekly status synthesizer** (Now) | Primary | Compresses her Friday from 90 minutes to 20. |
| **Release notes generator** (Now) | Primary | Three variants from one source artifact — her exact ask. |
| **Stakeholder comms tailoring** (Next) | Primary | The exec/Sales/CS variants she manually produces today. |
| **Story & ticket writer** (Now) | Secondary | She drafts tickets herself most days, but uses it for PRD-section bulk runs. |
| **Backlog grooming copilot** (Next) | Secondary | Useful, but Maya's growth backlog is high-churn; she dismisses many findings. Jordan is the primary user. |
| **Handoff runbook generator** (Later) | Secondary (as producer) | When a growth feature transfers to RTB, she's the producer who must hand off a complete runbook. Her interest is doing it fast and not getting paged afterward; Jordan ([RTB PO](./jordan-rtb-po.md)) is the quality bar. ([spec](../specs/handoff-runbook-generator.md)) |
| **PM knowledge agent** (Later) | Aspirational | When it exists, she expects to use it heavily for retros and "what did we try last time?". |

## Tools she lives in today

Linear, Confluence, Slack (3 channels she watches, 12 she half-watches), Notion (legacy docs), Loom (occasional), Figma (read-only), Google Calendar. She does not use a separate note-taking app — Slack DMs to herself stand in.

## Success looks like (for Maya)

| Metric | Definition |
|---|---|
| Weekly hours on status/release artifacts | Drops from ~5 to <2. |
| Time from "I need a PRD" to first reviewable draft | Drops from ~2 hours to <30 minutes. |
| Number of times per week she asks Sales/CS for "what did we say to the customer?" | Drops from 2–3 to <1. |
| Exec pings answered without source-hunting | Rises from rare to default. |
| Adoption | She uses the tools weekly, unprompted, by end of Q1 GA. |

## Failure modes (how our tools can lose Maya)

| Failure | What she does about it |
|---|---|
| A release note contains an unshipped feature. | She stops trusting release notes generation entirely; reverts to manual. Recovery takes a full quarter. |
| The Audience Tailor strips a real risk from an exec variant. | She loses confidence in the tailor for any audience. Reverts to copy-paste-edit. |
| Status synthesizer over-explains; she rewrites 80% of every draft. | She uses it twice and stops. Quietly. |
| PRD assistant suggests metrics that aren't measurable in her stack. | She files it under "AI tools that don't know our product." |
| Spine resolver returns the wrong epic. Downstream story writer fills a different team's backlog. | She files an incident. Trust hit is org-wide, not just hers. |

## Quotes (illustrative, not real)

> "If it's going to draft something for me, it had better be drafty enough that I'd actually edit it. Not a finished thing I have to redo."

> "I don't need another surface. I need Linear and Confluence to do this for me, in place."

> "The first time it makes something up in a release note, I am done. There is no second chance on that one."

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft of Maya — Senior Growth PM archetype. |
| 0.2 | 2026-05-28 | Lance | Updated Backlog grooming copilot row to cite Jordan (RTB PO) as the primary user; previously cited Devon (Platform PO), now retired. |
| 0.3 | 2026-05-30 | Lance | Added the handoff runbook generator to the tool-fit table as a producer-side tool (she hands growth features off to RTB). |
