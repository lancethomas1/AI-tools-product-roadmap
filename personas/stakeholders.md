# Stakeholder personas

The audiences that PM-authored artifacts get tailored for. These are not users of our tools — they are the recipients of what our tools produce. The [Audience Tailor agent](../governance/agent-library.md#6-audience-tailor) treats this file as its tone-and-framing reference.

## Principle

**Tailoring is reframing, not rewriting.** One source artifact becomes N audience variants. Facts stay constant; emphasis, length, and language change. A variant that adds a new fact, or strips a real risk to make a story sound better, is a bug, not a feature.

## Scope

| Audience | Where they receive PM artifacts | Primary stages where tailoring matters |
|---|---|---|
| [Executive](#executive) | Weekly briefings, exec syncs, board readouts | 3, 4 |
| [Sales](#sales) | Slack channels, enablement docs, release notes | 4 |
| [Engineering](#engineering) | Internal Slack/changelog, technical specs | 3, 4, 5 |
| [Customer Success](#customer-success) | CS team Slack, internal release notes, account-management docs | 4 |
| [Support](#support) | Support tooling macros, troubleshooting guides | 4, 5 |

Each section below defines: what they care about, what they consider noise, the must-preserve content when tailoring, and the anti-patterns an AI variant tends to introduce.

## How specs cite this file

A spec that produces audience variants (e.g., [Release notes generator](../ROADMAP.md), Stakeholder comms tailoring) links to the relevant section here for tone/length/risk-preservation rules. Spec content describes what the tool does; this file describes who it's writing for.

---

## Executive

**Role.** Director-level and above. Reads dozens of cross-org updates per week. Has 30 seconds for any one of them.

**What they care about**

- The outcome: did we win, lose, or is it still uncertain.
- The risk: what could derail this, what's the mitigation, what's the ask of them.
- The ask: do I need to do anything.

**What they consider noise**

- Process detail ("we held three refinement meetings").
- Activity-as-progress ("the team made significant progress on…").
- Cross-team color commentary they cannot act on.
- Hedging that hides the actual status.

**Must preserve when tailoring**

- **Every risk word.** If the source mentions "blocker," "delay," "slip," or "at risk," the exec variant retains the substance. The [Audience Tailor risk-preservation check](../governance/agent-library.md#6-audience-tailor) is named for this audience.
- **Numbers as-is.** No rounding that changes interpretation.
- **Explicit asks.** If the PM is asking for a decision, the exec variant says so in the first sentence.

**Length and shape**

- 3–6 sentences max for a status item, 1–2 paragraphs for a briefing.
- Lead with status, then risk, then ask. Detail is collapsible.
- Bullets are fine; bullets that read like a project plan are not.

**Anti-patterns the AI tends to introduce**

| Anti-pattern | Why it's wrong | Test |
|---|---|---|
| Burying the lead under context | Exec time-out before they reach the status. | First sentence must contain the status. |
| Stripping the risk to sound positive | Exec acts on bad signal. | Risk-preservation check (hard bar). |
| Vague verbs ("driving," "progressing") | Looks like progress, conveys nothing. | Reject any variant where >30% of verbs are vague. |
| Adding executive-summary cliché | Reads like a McKinsey deck. | Tone-similarity to negative-example golden. |

**Example transformation**

Source (Maya, weekly status):
> The personalization team had a tough week. The A/B test we expected to start Tuesday slipped to Friday because of a data-pipeline issue in the staging environment. Engineering thinks the fix is small but they want one more day to validate. We're still on track for the end-of-quarter launch but the buffer is thinner.

Exec variant:
> Personalization launch still on track for EOQ, buffer is thinner. A/B test slipped by 3 days due to a staging-env data-pipeline issue; eng confirms small fix. No ask. Flagging because if a second slip lands, EOQ moves.

Same facts. Status first, risk preserved, no buried lead.

---

## Sales

**Role.** AEs, SDRs, sales engineers. Talking to prospects and customers. Need a sentence they can use in a call within the hour.

**What they care about**

- Customer-facing impact: what does the prospect/customer see, do, or save.
- Timing: when is it available, what tier, what's the constraint.
- Objection handling: what will the customer push back on, what's the answer.
- Comparable: how does this map to a competitor or to what the customer asked for in their last RFP.

**What they consider noise**

- Internal team names, ticket IDs, repo references.
- Roadmap horizon labels ("Now," "Next," "Later") — they want a date or "rolling out X."
- Architecture detail unless it's a selling point.

**Must preserve when tailoring**

- **Availability and constraints.** If a feature is "GA for Enterprise, beta for Pro, not on Starter," all three clauses stay.
- **Customer-facing wording.** No internal codenames in a Sales variant — only the launched product name.
- **Honest scope.** No promising the v2 feature when only v1 ships.

**Length and shape**

- One headline (the talking point).
- One sentence on availability/constraints.
- 2–4 bullets on customer-visible impact.
- One sentence on the most likely objection + the answer.
- Total: under 200 words.

**Anti-patterns the AI tends to introduce**

| Anti-pattern | Why it's wrong | Test |
|---|---|---|
| Over-promising scope ("with this update, customers can…") | Sales sells the over-promise, support eats the difference. | Cross-check against the PRD in-scope list. |
| Internal codenames slipping through | Customer-facing reference to a name we never branded. | Strict allowlist of customer-visible names. |
| Generic-marketing tone ("revolutionize," "seamless," "best-in-class") | AEs find it useless; CMs ban it. | Negative-example golden. |
| Stripping the constraint to sound bigger ("now available" vs. "now in Enterprise GA, Pro beta") | Sales sells to a customer on the wrong tier. | Constraint-clause preservation check. |

**Example transformation**

Source:
> We shipped the saved-segments feature this week. Enterprise customers can now save up to 50 segments per workspace, with named tags and shared visibility across the workspace. Pro tier gets up to 10 segments without shared visibility. Starter tier is unchanged.

Sales variant:
> **Saved segments now GA on Enterprise, beta on Pro.**
> Available: Enterprise GA, Pro beta, not on Starter.
> Customer impact:
> - Enterprise: save up to 50 segments per workspace, named tags, shared with workspace.
> - Pro: save up to 10 segments, individual use only.
> Likely objection: "Can my Pro team share segments?" → Not in beta; on the roadmap for next quarter (not committed).

---

## Engineering

**Role.** Eng leads, senior engineers, eng managers on adjacent teams. Read PM updates to understand dependencies, sequencing, and architectural intent.

**What they care about**

- The technical decision and its rationale.
- Dependencies on their team.
- Migration/rollback implications.
- What this changes for an interface they consume.

**What they consider noise**

- Business framing they can't act on.
- Customer-facing marketing language.
- Status flavor ("we're really excited about this") with no technical substance.

**Must preserve when tailoring**

- **Technical specifics.** API changes, schema changes, contract changes, performance numbers — verbatim.
- **Dependency callouts.** If team X is named as a consumer or blocker in the source, the eng variant names them.
- **Rollback plan, if present.** If the source mentions rollback or migration safety, the eng variant includes it.

**Length and shape**

- A short paragraph or a tight bullet list.
- Technical detail dense; framing light.
- Always link to the ADR, RFC, or design doc.

**Anti-patterns the AI tends to introduce**

| Anti-pattern | Why it's wrong | Test |
|---|---|---|
| Translating technical detail into "business outcomes" | Strips the signal eng wants. | Preserve verbatim any line containing API/schema/contract terms. |
| Skipping the dependency callout because it's "downstream" | Adjacent team finds out at integration. | Dependency-name preservation check. |
| Adding marketing tone | Eng tunes out. | Tone-similarity check against eng-channel golden. |
| Hand-wavy on rollback | Eng won't trust a launch without it. | If "rollback" appears in source, must appear in eng variant. |

---

## Customer Success

**Role.** CSMs, account managers, customer onboarding. Manage active customer relationships. Need to know what changes for whom and what they should proactively communicate.

**What they care about**

- Which accounts are affected and how.
- What the customer experience changes look like.
- What CSMs should proactively reach out about.
- Training, documentation, or enablement they need to deliver.

**What they consider noise**

- Engineering implementation detail.
- Generic feature pitches that don't translate to a customer conversation.
- Anything that requires the CSM to translate "internal speak" into "customer speak."

**Must preserve when tailoring**

- **Affected-segment specificity.** "All Enterprise customers on plan X" — not "many of our customers."
- **Proactive vs. reactive.** If the source says "CSMs should reach out to top-20 accounts," the CS variant repeats that, not "consider reaching out."
- **Training/doc readiness.** Whether the help-center article is live, whether the runbook is updated.

**Length and shape**

- Headline: who is affected, what changes.
- Bullets: what to do (proactive outreach, training, FAQ updates).
- Link: customer-facing doc + internal runbook.

**Anti-patterns the AI tends to introduce**

| Anti-pattern | Why it's wrong | Test |
|---|---|---|
| Soft-pedaling a breaking change ("minor update to…") | CSMs don't reach out; customers are surprised. | Breaking-change words ("breaking," "deprecated," "removed") preserved hard. |
| Generic "let your customers know" | CSMs don't act on vague directives. | Action verbs with named segments preserved. |
| Vague affected-segment ("many users") | CSMs don't know who to call. | Source segment names preserved verbatim. |

---

## Support

**Role.** Support engineers, support agents, support leads. First-line response to customer questions and incidents.

**What they care about**

- What new questions/tickets to expect after a launch.
- Troubleshooting steps.
- When to escalate, and to whom.
- Known issues and workarounds.

**What they consider noise**

- Marketing framing.
- Strategic context.
- Anything they can't paste into a support reply or an internal macro.

**Must preserve when tailoring**

- **Symptom → fix mapping.** "If a customer sees X, do Y" stays literal.
- **Escalation paths.** Named owner, channel, or on-call rotation — verbatim.
- **Known limitations.** Don't soften "this won't work for accounts on plan Y" into "may not work for all accounts."

**Length and shape**

- A short situational summary.
- A bulleted FAQ or symptom/fix table.
- A clear escalation contact.

**Anti-patterns the AI tends to introduce**

| Anti-pattern | Why it's wrong | Test |
|---|---|---|
| Replacing literal customer-facing language with paraphrase | Support's macros need exact wording. | Quoted error messages and UI strings preserved verbatim. |
| Vague "contact the team" instead of a named owner | Tickets stall in escalation. | Owner-name/channel preserved. |
| Marketing softening of a known limitation | Support promises something they shouldn't. | Limitation words preserved. |

---

## Cross-audience rules

These rules apply regardless of audience and are enforced by the Audience Tailor agent.

1. **Fact addition is zero-tolerance.** A variant cannot introduce a fact not in the source. The agent's `source_diff` must show no added facts. (See agent contract: [Audience Tailor failure modes](../governance/agent-library.md#6-audience-tailor).)
2. **Risk preservation is hard.** Every risk/blocker/delay token in the source must appear in every variant; how it's framed may change, but the substance cannot vanish.
3. **Customer-facing variants (Sales, CS, Support) require PM sign-off before posting.** Internal variants (Exec, Engineering) can publish on PM review.
4. **Length-target compliance is enforced post-generation.** Over-budget triggers a retry, not a truncation that lops off the risk paragraph.
5. **Adding a new stakeholder.** Don't ship a one-off audience variant. Propose a new section in this file with the four required fields (cares-about / noise / must-preserve / anti-patterns), then update the Audience Tailor `audiences` enum.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft. Five audiences (Exec, Sales, Eng, CS, Support) with cares/noise/must-preserve/anti-pattern shape. |
