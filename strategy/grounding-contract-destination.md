# Grounding, Contract, Destination: Why a Toolset, Not a Chat

How the [roadmap](../ROADMAP.md) is shaped as a portfolio of integrated tools rather than "every PM gets a model seat and chats." A working position on the three structural properties that separate a roadmap tool from a chat session, and what we would lose by collapsing the toolset into the latter.

## Purpose

New contributors and skeptical stakeholders arrive at the roadmap asking the same question: *"Couldn't a PM just open a chat and ask for a PRD?"* The roadmap implicitly answers no, but the rationale lives scattered across the spine principle, the operating principles, the agent library, and the evaluation-personas hard bars. This document gathers the rationale into one place, so the answer is something a contributor can read in 10 minutes instead of inferring from six other docs.

It is also the doc to cite when a spec author is tempted to under-build — "the PM can just chat, why are we writing a tool here?" The answer is in the three properties below.

## Principle

**Raw chat produces prose. The toolset produces artifacts that downstream tools, downstream stakeholders, and the next quarter's PM can actually use.**

The difference rests on three structural properties no chat session has. The toolset is **grounded** in the company's data, ships under a **contract**, and ends at a **destination**. Each property is independently necessary; the three only deliver their value together. A tool that satisfies one or two and not the third is not a roadmap tool.

## 1. Grounding

The tool reads from the spine, the corpus, and the team's accepted style. The chat does not.

A PM asking raw chat for a PRD on "search in the mobile app" gets a polished doc grounded in nothing: generic users invented from search-feature lore, success metrics that aren't measurable in the team's stack, an in-scope list pulled from no source, zero citations. The same brief into the PRD Drafter reads the team's template, surfaces clarifying questions for the things only the PM knows (segment, infra, success bar), refuses to fabricate metrics, and produces an artifact downstream tools can find via the [shared spine](../ROADMAP.md#the-shared-spine).

What grounding requires:

- **Spine resolution** (Confluence PRD + Jira epic) so the artifact has a fixed locator and downstream tools can read it.
- **Per-team context**: PRD templates, accepted-ticket style, customer segments, in-flight epics, decision log.
- **Per-PM scope**: the tool respects the requesting PM's existing access to Slack channels, tickets, and docs — it never fans out beyond what they could read directly.

### Worked example: Sam's knowledge query

Sam asks "why did we deprecate the legacy onboarding flow in 2025?" Raw chat returns a confident, plausible answer (fabricated — it has never seen the decision log). The PM Knowledge Agent either returns the actual citation, or refuses with *"I found 0 sources in scope"* (see [adversarial: contradictory sources](../personas/evaluation-personas.md#adversarial-contradictory-sources)). **The refusal is the feature** — it stops the relitigation that a confidently-wrong chat answer would have triggered.

### Anti-pattern

A tool that "fills in" context the PM didn't supply. The spine principle says: fail loudly when the artifact can't be located. Helpfulness at the cost of grounding is the failure mode.

## 2. Contract

The tool runs against a published eval suite with hard bars. The chat doesn't.

A chat session is a best-effort collaboration. A roadmap tool is a service with guarantees the PM can plan around. The [agent library](../governance/agent-library.md) names the agents and their voice profiles; the [evaluation personas](../personas/evaluation-personas.md) name the inputs each agent must handle and the failure modes each must refuse to produce. The hard bars enforced in production:

| Hard bar | Why the PM relies on it |
|---|---|
| Fabricated-content rate on too-thin inputs: 0 | Priya can't submit a polished-but-wrong PRD — the assistant returns a clarifying-question list instead. |
| Customer-facing variants with unverified citations: 0 | Maya's release note never leaves Confluence with a hallucinated link. |
| Risk-preservation in exec variant: 1.0 | Sam's exec update never has a real slip silently softened by the Audience Tailor. |
| Auto-action rate by Auditor / Drift Detector: 0 | Jordan never finds a ticket auto-merged or auto-closed by the backlog grooming copilot. |
| PII-leak rate to model provider: 0 | A customer name in a meeting transcript never reaches the model. |
| Silent-stale-use rate: 0 | The Knowledge Retriever surfaces "index last refreshed N hours ago" alongside every answer. |

Raw chat offers none of these guarantees by construction. Each session is its own promise, made by the assistant in the moment, and verifiable only after the fact.

### Worked example: Priya's first PRD

Priya types a one-sentence brief into raw chat. It happily generates a polished PRD. Her manager rejects it Wednesday on first read — wrong template, no link to the company's customer segments, success metric unmeasurable in their stack. Priya has spent two days on something her manager could see was wrong in two minutes.

The same brief into the PRD Drafter. The contract says: too thin to draft → return clarifying questions. She gets a checklist of the five things she needed to think about. The refusal teaches her the shape of the artifact. Raw chat would have hidden it. This is the failure mode `personas/priya-new-pm.md:93` calls *"the most dangerous failure mode of all"*: opaque polished output the PM submits without learning anything.

### Anti-pattern

A tool that breaks its contract "to be helpful." *"I'll make a best guess on the metric"* is exactly the failure the eval suite forbids. The contract has no exceptions — a tool that ships an exception is operating outside the roadmap.

## 3. Destination

The tool ships into the surface the PM (or the downstream stakeholder) already uses. The chat hands them a transcript.

Even if a chat produced a perfect PRD, the PM still has to copy-paste it into Confluence, retype the in-scope list into Linear, format the announcement for Slack. The PM becomes the integration glue. Operating principles 5 (*integrate, don't aggregate*) and 6 (*lifecycle, not feature type*) compose into a single design rule: each tool's destination is the next tool's input. The four patterns the existing specs use:

| Pattern | Where the artifact lands |
|---|---|
| Embedded action | Confluence draft page · Jira/Linear ticket · sidebar comment on a PRD |
| Slash command | Slack thread with a link to the draft |
| Scheduled / triggered agent | Confluence draft · Slack DM · inline comment, with no PM action at the point of generation |
| Conversational query | Web surface or `/recall` (reserved for the PM Knowledge Agent — see § "When chat is the right tool" below) |

Every spec's "UX surfaces" section picks from these and ends with the line *"No standalone app surface (operating principle 5)."* That line is not boilerplate — it's the destination commitment.

### Worked example: Maya's Friday status

Raw chat: Maya types a prompt at 4:30pm Friday, gets prose, copies it into a Google Doc, pastes into Slack for her squad, reformats by hand for the exec variant, then again for CS. Thirty minutes minimum, three windows.

Status synthesizer + Audience Tailor: scheduled DM at 3pm Friday with a link to the draft already grounded in the week's Jira/Slack/GitHub activity; she edits in Confluence; the Tailor produces exec/Sales/CS variants from her one approved source. Under twenty minutes, one surface. Risk-preservation hard bar guarantees the exec variant didn't strip the buffer-thinned-after-slip line she put in the source.

### Anti-pattern

A tool whose output sits in a chat window the PM has to relocate. That's not a destination — it's a holding cell. The grounding and contract work the tool did doesn't survive the copy-paste; the PM can introduce errors, mis-format, or simply not send it.

## How the three compose

The properties only deliver their value together. Each pair without the third is a failure mode:

| Missing | What the partial tool does | Why it fails |
|---|---|---|
| Grounding | Reads nothing real but refuses to fabricate, and ships to a real destination | Refuses everything. Functionally useless. |
| Contract | Reads the spine and ships to Confluence, but happily fabricates a metric | Worse than chat — chat at least doesn't pretend to know the team's data. A grounded fabrication is more dangerous than an ungrounded one. |
| Destination | Reads the spine, refuses to fabricate, but returns prose in a chat | The PM relocates the output by hand. Eval guarantees don't survive the copy-paste. Effectively a chat with extra steps. |

Every roadmap spec must satisfy all three. A spec that addresses only one or two is a script, not a roadmap tool.

## Where each property is enforced

| Property | Mechanism | Governing doc |
|---|---|---|
| Grounding | Spine principle; per-PM access scoping; PII Scrubber on ingress | [ROADMAP.md § The shared spine](../ROADMAP.md#the-shared-spine) · [governance/security-data-envelope.md](../governance/security-data-envelope.md) |
| Contract | Agent library voice profiles; eval-persona hard bars; Citation Verifier | [governance/agent-library.md](../governance/agent-library.md) · [personas/evaluation-personas.md](../personas/evaluation-personas.md) |
| Destination | Four interaction patterns; no auto-publish; integration-not-aggregation | [ROADMAP.md § Operating principles](../ROADMAP.md#operating-principles) (#5, #6); per-spec "UX surfaces" section |

## When chat is the right tool

This document doesn't argue that PMs should never chat with a model. It argues against chat as the substrate of the roadmap. Chat is the right tool for:

- **Pre-brief thinking**, before the spine exists and there's nothing to ground against.
- **Throwaway brainstorming** with no audience, no review chain, no downstream consumer.
- **Open-ended exploration** where producing an artifact would be premature.

These uses fall outside the lifecycle pipeline. A champion's [Claude Code seat](./licensed-seats.md) covers them; the roadmap doesn't try to replace them. The one place a chat-shaped interaction appears *inside* the roadmap is the PM Knowledge Agent — and only because its job (query → cited answer, follow up, refine) doesn't have a natural home inside Confluence or Linear.

## Failure modes this document forecloses

| Failure | What it looks like in a review | What this doc says |
|---|---|---|
| "We don't need to build this; PMs can just chat." | A spec is postponed because raw chat is "close enough." | Close enough on grounding only. No contract, no destination. The downstream cost lands on the persona who reads the chat output and submits it. |
| "Let's add a chat surface to every tool." | A spec proposes a chat UI alongside its plugin/command surfaces. | Chat is a runtime, not a UI. The conversational pattern is reserved for the PM Knowledge Agent. |
| "The tool can fabricate to be helpful." | An agent proposes filling in a missing metric "to save the PM time." | Contract violation. Refusal is the feature. |
| "The tool's output is a transcript; the PM can copy from there." | A spec ships without an integrated destination. | Not a roadmap tool. Operating principle 5. |
| "Grounding is enough; we can skip the eval contract for v1." | A spec defers eval-suite work to a later release. | A grounded fabrication is more dangerous than an ungrounded one. Contract is non-deferrable. |

## Status

Working position. Promote to `governance/` once the rationale is cited routinely by spec authors and reviewers without contention. Until then, this is the doc to point at when the question "why not just chat?" comes up.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-28 | Lance | Initial draft. Articulates grounding, contract, and destination as the three structural properties separating a roadmap tool from raw chat. |
