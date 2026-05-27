# Contributor Model

How a user of a product-owned tool becomes a contributor to it, and how the existing governance docs read once that path is named.

## Purpose

The other governance docs each answer a piece of the same question without ever stating the whole. The [ownership model](./ownership-model.md) names the one accountable owner. The [approval model](./approval-model.md) defines tiers of change. The [engineering guardrails](./engineering-guardrails.md) and [security envelope](./security-data-envelope.md) define what no contributor — PM or engineer — may violate. The [licensed-seats strategy](../strategy/licensed-seats.md) introduces the "champion" without ever giving the role a home in governance.

This document is the missing connective tissue: it names the rungs that users climb on a product-owned tool, what each rung is allowed to do, and how the other governance docs become legible once you have that ladder in front of you. It does not invent new constraints. It explains the constraints that already exist.

## Principle

**On a product-owned tool, the user and the contributor are drawn from the same pool. Governance exists to make that safe without requiring an engineering escort for every change.**

If users and contributors had to be different people, every PM-built tool would re-introduce the engineering bottleneck the [ownership shift](./ownership-model.md) was meant to remove. The rails — guardrails, envelope, approval tiers — exist so the same PM can use a tool on Monday, fix a prompt on Tuesday, approve a peer's PR on Wednesday, and own the on-call on Thursday, without any of those rungs requiring a different person.

## Scope

Applies to every tool classified product-owned under the [ownership model](./ownership-model.md). Engineering-owned tools follow engineering's own contribution practices; the rungs below are not binding on them.

The rungs describe roles played on a single tool. The same person plays different rungs on different tools. A PM is the Tool Owner of the release-notes generator, a Champion of the PRD assistant, a Contributor on the backlog grooming copilot, and a plain User of the meeting → artifact pipeline. None of those conflict.

## The four rungs

| Rung | What they do | Surface they touch | Approval tier they can self-serve |
|---|---|---|---|
| **User** | Runs the tool as it ships. Files bug reports, requests, and "the output was wrong" feedback. | The tool's UX (Jira plugin, Slack command, Claude Code skill). No PR access required. | None — they file issues, they don't merge. |
| **Champion** | Power-user for their squad. Curates templates and prompt fragments their team relies on, runs interactive tools on their own Claude Code seat, evangelizes the tool inside the squad. | Tool config in their checkout; small T0 PRs on behalf of their squad. | [T0](./approval-model.md#t0--self-serve-no-human-review). |
| **Contributor** | Opens PRs that change the tool's behavior. Fixes a prompt, adds a template variant, lands a new feature inside the tool's boundary. | T0 and T1 PRs against the tool's repo. | [T0](./approval-model.md#t0--self-serve-no-human-review) and [T1](./approval-model.md#t1--peer-review-within-product) (the latter with one peer PM review). |
| **Tool Owner** | Accountable for the tool overall. Triages issues, approves T1 PRs from peers, runs the help channel, escalates T2+ to engineering, owns the metrics and the roadmap. | Everything above plus the gate. One named PM per tool. | All product-managed tiers. T2/T3 escalate per the [approval model](./approval-model.md). |

The rungs are cumulative: a Tool Owner is also a Contributor, also a Champion, also a User. The ladder is one-way only in that direction — a User cannot grant themselves T1 merge rights without first becoming a Contributor.

### User

The default rung. Every PM in the target audience of a product-owned tool is a User of it by virtue of being in the audience. The User rung has no provisioning step beyond ordinary access to the SaaS surfaces the tool integrates with (Jira, Confluence, Slack, etc.).

Users are the source of the tool's most important signal. The tool's repo, its `#pm-toolkit-ops` channel, and its weekly-status touchpoint are User-facing surfaces. Treating User feedback as background noise is one of the failure modes called out below.

### Champion

A Champion is a User who has invested enough hours to materially shape how the tool gets used inside their squad. Three markers, taken together:

1. **A Claude Code seat** (or the equivalent runtime license for the tool in question). The [licensed-seats strategy](../strategy/licensed-seats.md) defines what scarcity at this rung looks like — the Champion rung is where seat allocation becomes operational.
2. **Local config they maintain.** A squad-specific template, a CLAUDE.md fragment, a saved prompt — small artifacts that customize the tool for their team and that they keep current.
3. **A squad that consumes their output.** A Champion who produces only for themselves is a User with a seat, not yet a Champion.

Champions are the *first responders* to User feedback on tools they champion: a complaint about the PRD assistant from a squad-mate goes to the squad's Champion before it goes to the Tool Owner.

A separate planned doc — `governance/champion-charter.md`, referenced from [licensed-seats](../strategy/licensed-seats.md) — covers the operational contract for a Champion (maintenance expectations, office-hours requirement, on-call for batch agents serving the squad). The contributor model defines the rung; the charter defines the duties.

### Contributor

A Contributor is a Champion (or any PM) who has opened and landed a PR against the tool's repo. The first merged PR is the moment a Champion becomes a Contributor; there is no separate provisioning event.

Contributors operate inside the [approval model](./approval-model.md): T0 changes self-merge, T1 changes need one peer PM review, T2+ escalate to engineering. A Contributor is not authorized to assign the `engineering-review` label that the CI gate requires for T2 merges — that label, like the rung above it, stays with engineering reviewers.

Contributors are not pseudonymous. A Contributor's identity on PRs is their real org identity, and their merged history is the recorded record of their contributions for promotion to Tool Owner.

### Tool Owner

The single named PM accountable for the tool. There is exactly one Tool Owner per tool. Co-ownership is not a thing — co-owned tools have nobody on call.

The Tool Owner approves T1 PRs from peer Contributors. They cannot approve their own PRs at T1; a peer Contributor on the team does that. They are accountable for the [success metrics](./ownership-model.md#what-ownership-carries) defined for the tool, for triaging the issue backlog, and for being the incident commander on envelope-touching incidents per the [security envelope](./security-data-envelope.md#incident-response).

Tool Owner is a real workload. The anti-pattern of one PM owning four tools is called out in [Failure modes](#failure-modes--mitigations) below.

## How the rungs map to existing governance

The four governance docs each address a different rung's concern. Re-reading them through the contributor model:

| Question | Doc | What it answers, in contributor-model terms |
|---|---|---|
| Who is the named Tool Owner, and on what basis? | [ownership-model](./ownership-model.md) | Tool Owner is "whoever owns the end user." |
| What can each rung self-serve on without escalating? | [approval-model](./approval-model.md) | T0 is the Champion lane; T0/T1 is the Contributor lane; T2/T3 escalate out of the PM pool. |
| What holds regardless of which rung makes the change? | [engineering-guardrails](./engineering-guardrails.md) | The rails that make broadening the contributor pool safe. |
| What no rung may violate? | [security envelope](./security-data-envelope.md) | The constants. The envelope does not move when the rung changes. |
| What composable parts do Contributors build with? | [agent-library](./agent-library.md) | The kit that keeps new tools from becoming new platforms. |
| Where does the contributor pool have commit access by default? | [github-org-structure](./github-org-structure.md) | The `rcg-digital-product` org. The ladder is visible at the repo URL. |

None of those docs needs to change for this model to be true. They were already organized around it; this doc names the organizing principle.

## Movement between rungs

| Transition | What triggers it | What's recorded |
|---|---|---|
| **User → Champion** | A seat allocation under the [licensed-seats strategy](../strategy/licensed-seats.md), plus the Champion taking up squad-local config they maintain. | The Champion's name in the tool's README under "Champions per squad" (added when the tool has its first Champion). |
| **Champion → Contributor** | First merged PR against the tool's repo. No separate provisioning step. | The PR. Git history is the record. |
| **Contributor → Tool Owner** | Sponsorship by the current Tool Owner and product leadership, with a written handoff covering metrics, backlog state, known issues, and on-call posture. | A note in the tool's README updating the Tool Owner field; an entry in the tool's version-history-style log. |
| **Tool Owner → Tool Owner (handoff)** | Same as above, plus a 30-day overlap where the outgoing owner remains incident commander while the incoming owner shadows. | The handoff note. |
| **Any rung → demoted** | Sustained inactivity, sustained guardrail violations, or role change out of the product org. | Same surfaces, marked inactive. Demotion from Tool Owner is a deliberate act, not drift — see the [escalation path](./ownership-model.md#escalation). |

The direction matters: rungs are climbed by demonstrating the work, not by being granted permission to do future work. A PM does not become a Contributor by being given Contributor status; they become a Contributor by landing a PR.

## Worked examples

### Example 1: A new PM uses the PRD assistant and reports a wrong-output case

Rung: **User.** Files an issue in `rcg-digital-product/pm-toolkit` with the input that produced the bad output. No PR. The squad's Champion picks the issue up; the Tool Owner sees it on the weekly triage. No governance action required — this is the system working.

### Example 2: A Champion adds a squad-specific PRD template

Rung: **Champion.** Maintains a `templates/squad-payments.md` in their checkout for two months, finds it useful, opens a T0 PR adding it to the shared toolkit with a short README note on when to use it. Becomes a **Contributor** at the merge. No reviewer required — T0 self-merge after CI.

### Example 3: A Contributor proposes a new "generate weekly digest" feature on the status synthesizer

Rung: **Contributor**, opening a T1 PR. The Tool Owner reviews and merges. The feature stays inside the tool's boundary; no engineering review fires.

### Example 4: Tool Owner handoff for the release-notes generator

The outgoing Tool Owner has been on the tool for three quarters and is rotating into a new role. The handoff:

1. Names the incoming PM (a current Contributor on the tool with three merged PRs).
2. Product leadership signs off.
3. README updated, 30-day overlap begins, incoming owner shadows the next two release cycles.
4. Recorded under `governance/decisions/` (created on first use).

Engineering is notified but does not approve — the [ownership model](./ownership-model.md) places this decision in product.

### Example 5: A Champion's squad-local template is accidentally relied on by another squad

Rung confusion. The template wasn't merged to the shared toolkit; it sat in one squad's checkout and was copy-pasted into another's. The Champion's options: open a T0 PR promoting it to the shared toolkit (becoming a Contributor in the act), or document that it's squad-local and shouldn't be lifted. Either resolves the drift. Doing neither is the failure mode.

### Example 6: A Contributor's PR touches the CI workflow

Rung breach. CI config is a [T2 change](./approval-model.md#t2--engineering-review-required). The Contributor cannot self-merge it; an engineering reviewer applies the `engineering-review` label and the merge gate honors it. The Contributor doesn't get a new rung — they're still a Contributor — they just need a reviewer from outside the PM pool for this PR.

## Failure modes & mitigations

| Failure mode | What goes wrong | Mitigation |
|---|---|---|
| **Shadow ownership** | A Champion is effectively maintaining a tool — fielding bugs, doing the on-call — but the README still names a Tool Owner who has rotated out. | README field is authoritative. Quarterly Tool Owner review by product leadership. If the named owner is inactive, promote the de facto owner or formally rotate. |
| **Owner overload** | One PM owns four tools because they were the first PM to touch each. | Soft cap of two tools per Tool Owner. A third requires explicit product-leadership sign-off. |
| **Engineering-as-default contributor** | A PM-owned tool ends up with engineering merging the majority of PRs because no PM steps up. | This is a signal the tool is engineering-owned in practice. Re-classify under the [ownership model](./ownership-model.md#boundary-cases). |
| **Contribution without using** | A Contributor who never uses the tool merges PRs based on what they think users want. Output drifts from real use. | Tool Owner enforces "Users first" — a Contributor's PRs lose review priority if the Contributor isn't also a User of the tool. |
| **User feedback dropped on the floor** | Users file issues into a repo nobody triages. | Weekly triage is a Tool Owner duty. Triage SLA recorded in the tool's README. Aged unanswered issues are a metric the Tool Owner reports. |
| **Promotion-by-grant** | A PM is named Tool Owner without first being a Contributor on the tool. | Promotion to Tool Owner requires a documented contribution history. A handoff to a complete outsider is a code smell that needs explicit justification. |
| **Caste-by-seat** | The Champion rung calcifies into a permanent in-group because seats never rotate. | The [licensed-seats](../strategy/licensed-seats.md) re-allocation review (every two quarters) is the formal countermeasure. The office-hours requirement in the planned champion charter is the cultural one. |
| **Cross-tool authority creep** | A Tool Owner of one tool reviews and merges on a tool they don't own because they "know the code." | T1 reviewer must be a Contributor on *that* tool. Cross-tool review is allowed and useful; cross-tool *approval* is not. |

## Anti-goals

- **A formal certification step between rungs.** The rungs are evidenced by work, not by approval to do future work. We do not add a "Contributor onboarding" checklist that gates first PRs.
- **Title inflation.** "Tool Owner" is a workload, not a promotion. We do not list Tool Owner roles on performance reviews as standalone credentials; we list the tool's outcomes.
- **A separate engineer rung.** Engineering reviewers on T2/T3 changes are not "Senior Contributors." They are engineering, applying engineering review. The rungs in this doc describe the PM pool only.
- **Per-tool variation in the rungs.** Every product-owned tool uses the same four-rung model. A tool that needs different rungs is a sign the tool isn't product-owned.

## Roles

| Role | Responsibility |
|---|---|
| **Product leadership** | Approves Tool Owner promotions and handoffs. Reviews the rung map quarterly to catch shadow ownership and overload. |
| **Tool Owner** | Maintains the README rung map for their tool. Triages User feedback. Approves T1 Contributor PRs. |
| **Champion** | First responder to squad-local User feedback. Maintains squad-local config. Becomes a Contributor by landing PRs. |
| **Contributor** | Opens PRs at the appropriate tier. Reviews peer T1 PRs on tools they contribute to. |
| **User** | Files issues against the tools they use. Asks for what's missing. |
| **Engineering reviewer** | Applies the `engineering-review` label on T2 PRs. Not a rung in this model — sits outside the PM pool. |

## Review cadence

Reviewed every 6 months by product leadership alongside the [ownership model](./ownership-model.md). The two docs share a review window because changes to one usually imply changes to the other.

Outside cadence, the model is re-examined when:

- A new tool's first Tool Owner can't be identified from the existing Contributor pool. (Signals a gap in the ladder.)
- A failure mode from the table above is observed twice on the same tool. (Signals the mitigation isn't enough.)
- A Tier B [engineering platform investment](../strategy/engineering-optional.md) changes who can contribute (e.g., an SSO'd web surface invites non-PM contributors). (Signals the pool definition is shifting.)

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft. Names the User / Champion / Contributor / Tool Owner ladder and maps it onto the existing governance docs. |
