# Licensed Seats: Roadmap and Distribution Under Seat Scarcity

How the roadmap, the build approach, and the governance model change when Claude Code seats — or any expensive per-human AI license — are scarce and have to be allocated deliberately rather than handed to every PM.

## Purpose

The [roadmap](../ROADMAP.md) currently assumes broad self-serve PM access. The success metric is ">70% weekly active PM users." That assumption holds only if every PM has the runtime to invoke every tool. Under seat scarcity it breaks, and the roadmap's shape has to change with it.

This document spells out what changes, why, and what the distribution model should be. It is a working position, not policy.

## Principle

**Under seat scarcity, the unit of distribution stops being "a tool a PM uses" and becomes "a seat that produces artifacts other PMs consume."** Every roadmap item has to be re-examined against that shift. Some become cheaper to build. Some become harder to distribute. Some don't compete for seats at all because they run as agents on a service identity, not at a human's keyboard.

## The reframe

Without seat scarcity, the roadmap's tools are *services* a PM uses directly: a PRD assistant, a ticket writer, a release notes generator, each potentially with its own surface or integration.

With seat scarcity, the roadmap's tools are **skills, prompts, agents, and CLAUDE.md context** that live in a shared product repo. The seat is the runtime. The "tool" is the curated configuration the licensed PM points the runtime at.

Concretely, a licensed PM running `claude` inside a `rcg-digital-product/pm-toolkit` repo (per [github-org-structure](../governance/github-org-structure.md)) gets every roadmap capability as a slash command, skill, or agent in that one runtime. A non-licensed PM never invokes the tool directly — they receive the artifact it produced: a PRD draft in Confluence, tickets in Jira, release notes in Slack.

This is actually **cheaper to build than the current roadmap**, because the entire UI and integration layer for licensed PMs collapses into the Claude Code runtime they already have. Custom surfaces are only built where non-licensed consumers need direct contact with the tool — typically Slack bots, Jira plugins, or scheduled posters.

## The split that matters: human seat vs. agent identity

Not every tool needs a human at the keyboard. The roadmap cleaves naturally into two halves:

### Needs a human Claude Code seat (interactive)

| Tool | Stage | Why it needs a seat |
|---|---|---|
| PRD drafting assistant | 1 — Drafting | PM iterates with the model in real time on framing and structure. |
| Story & ticket writer | 2 — Planning | PM applies judgment to acceptance criteria and sizing. |
| Spec → sprint decomposer | 2 — Planning | PM negotiates trade-offs with engineering. |
| Backlog grooming copilot | 3 — Execution | PM judges duplicates, merges, priority calls in session. |
| Meeting → artifact pipeline | 3 — Execution | PM attributes who-said-what and resolves ambiguity. |
| Stakeholder comms tailoring | 4 — Release | PM tunes tone and detail per audience. |
| PM knowledge agent | 5 — Post-release | PM asks ad-hoc questions, follows threads. |

### Runs as a scheduled or triggered agent (no seat required)

| Tool | Stage | Trigger |
|---|---|---|
| Release notes generator | 4 — Release | Epic state change / merged PRs against the epic. |
| Weekly status synthesizer | 3 — Execution | Cron, end of week. |
| Cross-functional launch checklist | 4 — Release | Epic state / launch date proximity. |
| Living spec sync | 5 — Post-release | PR webhook on tickets under the epic. |
| Proactive sprint agent | 3 — Execution | Daily sprint state watcher. |

The right-column tools run under a shared service identity against the API. They don't compete for seats. **Roughly half the roadmap is unaffected by seat scarcity** — a more optimistic read than first impression suggests.

The implication for prioritization: under scarcity, the batch/agentic tools become **more valuable per build effort**, because their leverage reaches every PM (including non-licensed ones) automatically. Items like the cross-functional launch checklist and the weekly status synthesizer move up.

## Distribution models for the scarce seats

Three reasonable ways to allocate the human seats:

### Champions model (recommended)

3–5 PM "power users" get seats and become the de facto producers of drafts and curators of the shared toolkit repo.

- **Bet.** Deep usage by a few drives quality, and the toolkit repo compounds because the same people maintain it across many reps.
- **Why this wins early.** The toolkit is empty on day one. Someone needs enough hours in the runtime to make the skills good. Spreading thin gets you a shallow toolkit nobody trusts.
- **Risk.** Permanent caste. Non-licensed PMs feel gated out. Mitigation: publish the artifact production volume per champion, document the path from "consumer" to "champion," and run an explicit re-allocation review every two quarters.

### Rotation model

Seats rotate quarterly through PMs who opt in to learn the runtime.

- **Bet.** Broader buy-in, no permanent in-group.
- **Risk.** Shallow usage. Each rotation onboards from zero. The toolkit repo never matures because every operator is new. Defensible only after the toolkit is stable.

### Lifecycle-stage model

Seats follow features through the pipeline — drafting seats to PMs in Stage 1, release seats to PMs in Stage 4.

- **Bet.** Matches the roadmap's lifecycle organizing principle.
- **Risk.** PMs don't live cleanly inside one stage. Assignment overhead. Practical only if a PM's portfolio aligns tightly with stage boundaries, which is rare.

**Recommendation.** Champions model for the first two quarters. Re-evaluate at month six against the toolkit's maturity, champion burnout signals, and the equity complaint volume. Rotation becomes viable once the toolkit is good enough to onboard a new operator in a week.

## What changes in the roadmap

### Structural changes

- **Most "Now" tools collapse into one deliverable.** The PRD assistant, the ticket writer, the release notes generator, and the status synthesizer aren't four separate apps — they're `/draft-prd`, `/write-tickets`, plus two scheduled agents in a single `pm-toolkit` repo. The repo, not the individual tools, becomes the unit of work.
- **Batch/agentic tools get earlier dates.** Cross-functional launch checklist agent and weekly status synthesizer move up because their leverage reaches non-licensed PMs and they don't consume the scarce resource.
- **Anything that was going to need a bespoke UI is reconsidered.** If the licensed PM uses Claude Code as the UI, and the non-licensed PM receives a finished artifact in their existing tool, the only UIs we build are for non-licensed-PM consumption surfaces (Slack post formats, Confluence page layouts).

### Metric changes

The current success metrics assume direct adoption. Under seat scarcity, replace them:

| Old metric | New metric |
|---|---|
| Weekly active PM users | Artifacts produced per seat per week |
| Tool adoption (>70% PMs) | Non-licensed PMs whose work consumed an artifact in the last 30 days |
| Time-to-first-draft (PM-direct) | Time-to-first-draft (champion's queue, end-to-end including handoff) |

The second metric is the equity check. If non-licensed PMs aren't consuming artifacts, the seats aren't producing leverage — they're producing personal productivity for the champions, which is a much weaker case.

### Reprioritized "Now" list under scarcity

A scarcity-shaped Now list, in priority order:

1. **`pm-toolkit` repo with PRD drafting + ticket writing skills.** The minimum viable champion runtime.
2. **Release notes generator (agentic).** First batch tool. Highest visibility per build hour.
3. **Weekly status synthesizer (agentic).** Reaches every PM without a seat.
4. **Cross-functional launch checklist agent (agentic).** Was "Later" in the un-scarce roadmap; promote because it doesn't consume seats and serves non-licensed PMs.
5. **Backlog grooming copilot (skill in toolkit).** First interactive tool that demonstrates compound value from the toolkit pattern.

Stakeholder comms tailoring, spec → sprint decomposer, and PM knowledge agent stay in Next. Living spec sync and proactive sprint agent stay in Later.

## Governance implications

This strategy puts unusual weight on a few governance surfaces:

### The toolkit repo

`rcg-digital-product/pm-toolkit` becomes the org's most-edited repo by a wide margin. Every skill update, prompt change, and CLAUDE.md tweak goes through it. The [approval model's](../governance/approval-model.md) T0 self-serve tier is doing a lot of work — that's intentional, but it needs to be working in practice before champions are productive.

The repo's structure should make the skill ↔ tool mapping explicit:

```
pm-toolkit/
  .claude/
    skills/
      draft-prd/
      write-tickets/
      groom-backlog/
      tailor-comms/
      decompose-spec/
      meeting-to-artifact/
    commands/
  agents/
    release-notes/      # batch agent
    weekly-status/      # batch agent
    launch-checklist/   # batch agent
    living-spec-sync/   # batch agent
  CLAUDE.md
  README.md
```

Champions edit `skills/` directly. Batch agents in `agents/` deploy on the standard pipeline per [engineering-guardrails](../governance/engineering-guardrails.md).

### Champion charter

Licensed PMs need a clear contract for what the seat carries. A short charter doc (TBD as `governance/champion-charter.md` if this strategy is adopted) covers:

- Maintain the skills you use — update prompts and CLAUDE.md context when patterns drift.
- Share artifacts back to the requesting PM through the standard surfaces (PRD link in Confluence, ticket link in Jira), never in a private Slack DM that doesn't leave a trail.
- On-call for the batch agents your team depends on, per the [ownership model](../governance/ownership-model.md).
- Office hours: at least one weekly slot where a non-licensed PM can request a draft and watch the champion produce it.

The office hours requirement is the cultural countermeasure to the caste risk. Non-licensed PMs don't just receive artifacts; they see the runtime work.

### Agent identity ownership

The batch agents run under a shared service identity. The [ownership model](../governance/ownership-model.md) needs an entry for non-human owners: the service identity is owned by a named PM, the on-call for that agent is that PM, and the API key's billing and rate-limit allocation are recorded against that owner. This is a small extension to the ownership doc, not a new model.

## Risks and the case to leadership

Scarce seats give you a **better-built but more concentrated tooling layer**. The honest pitch:

- **Quality goes up** because a few PMs build serious reps with the runtime and maintain a repo that compounds.
- **Equity goes down** because non-licensed PMs depend on a champion's queue for anything interactive. The agentic half of the roadmap is the mitigation — it serves everyone.
- **The "why does Heather get Claude Code and I don't?" question is real** and the answer needs to already exist when it's asked. The champion charter, the office hours, and the equity metric (artifacts consumed by non-licensed PMs) are the answer. If those aren't credible, the seat allocation isn't credible.

The argument *against* scarcity isn't "everyone deserves a seat." It's "if you can afford it, broad seats let the toolkit get built by everyone simultaneously instead of by a champion's free hours." That's a real argument, and the budget conversation should engage it directly rather than pretending scarcity is a free choice.

## Open questions

- **How many seats?** 3–5 is a guess. Anchor to PM headcount and to the rate at which the champions can keep the toolkit current. Re-evaluate at month three.
- **Does engineering get seats?** Out of scope here — engineering's seat allocation is engineering's call. But the toolkit repo's CI and observability are engineering-built, so engineering needs at least platform-side access to the runtime.
- **Reclassification trigger.** If a batch agent's failure starts reaching customers, it's no longer the same kind of asset; ownership flips per the [ownership model](../governance/ownership-model.md) and the seat strategy doesn't apply.
- **Tools we shouldn't build under scarcity.** Worth a follow-up pass: any roadmap item whose value depends on a non-licensed PM invoking it directly is in trouble under this model. Re-examine and decide.

## Next steps

If this position holds up to review:

1. Promote to a roadmap addendum in `ROADMAP.md`, replacing the broad-adoption metric language with the scarcity-shaped versions above.
2. Add the agent-identity entry to [ownership-model](../governance/ownership-model.md).
3. Draft the champion charter in `governance/`.
4. Create `rcg-digital-product/pm-toolkit` per the threshold in [github-org-structure](../governance/github-org-structure.md) — the scarcity model accelerates the threshold rather than delaying it.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-26 | Lance | Initial draft. |
