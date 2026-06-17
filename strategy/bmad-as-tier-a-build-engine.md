# BMAD as a Tier A Build Engine

How an external agentic-development framework (BMAD) fits this program: as a build-time engine for the self-serve, low-blast-radius internal tools that central engineering, design, and delivery will not staff — not as our operating model. A working position on adopting BMAD inside the [engineering-optional](./engineering-optional.md) lane, with the boundary drawn so velocity does not outrun the rails.

## Purpose

The question this document answers came up directly: "Am I reinventing the wheel relative to BMAD?" [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) is a widely adopted open-source framework that organizes AI agents into agile roles (Analyst, PM, Architect, UX, Scrum Master, Dev, QA) and runs them through an Analysis → Planning → Solutioning → Implementation cycle to produce working software. It overlaps with this program at the agent layer and at the Stage 1–2 artifacts (PRD, stories), and nowhere else.

The honest read is that BMAD is one level *below* this repo. BMAD answers "how does one builder get an AI to ship software in a disciplined way?" This repo answers "what portfolio do we build, who owns each tool, what data may it touch, and how do tools chain on our rails?" Those are different problems. The risk is not that BMAD makes this repo redundant; it is that we either ignore a useful engine or adopt it past the point where it fits. This document draws the line.

## Principle

**BMAD is a build-time engine for tools that pass the Tier A data declaration; it is not a run-time owner and it is not our operating model.** Use it to get one builder from brief to working artifact fast. Hand the artifact to our spine and governance for its life.

## Scope

Applies to the decision of whether and how to adopt BMAD (or an equivalent single-operator agentic-development framework) for tools delivered under [engineering-optional](./engineering-optional.md). It does not change the [ownership model](../governance/ownership-model.md), the [security & data envelope](../governance/security-data-envelope.md), or the [approval model](../governance/approval-model.md); it sits inside them.

## What BMAD assumes, and why it matters

BMAD assumes **a single human operator** driving the full agent cast in one session. The "team" is a team of AI personas wearing one person's hat in sequence, not a structure multiple humans plug into. The evidence is structural, not incidental:

- Generated artifacts hardcode absolute, single-user paths (e.g. `/home/<user>/dev/project/...` in `epics.md` and `ux-design-specification.md` frontmatter), which break the moment a second human shares the repo.
- "Party Mode" puts several *agents* in one conversation — still one human in the room.
- The explicit GitHub discussion ["A way to use BMAD with multiple human team members?"](https://github.com/bmad-code-org/BMAD-METHOD/discussions/1617) is largely unanswered; no official enterprise/multi-human pattern exists.

This single-operator assumption is exactly why BMAD fits *build time* and not *run time*. One champion spinning up a tool is BMAD's sweet spot. A tool with multiple maintainers and users over its life is where the assumption breaks — and where our spine, ownership model, and approval tiers take over.

## The eligibility gate: the Tier A data declaration, not priority

"Engineering won't staff it because it's not a priority" is the *motivation* for the lane, not the *eligibility test* for using BMAD. Priority and blast radius are different axes:

| | High priority | Low priority |
|---|---|---|
| **Low blast radius** | BMAD/Tier A candidate (e.g. a PM-facing utility that writes only to Jira/Confluence/Slack) | BMAD/Tier A candidate — the canonical long-tail case |
| **High blast radius** | Tier B; central staffing; BMAD's speed is irrelevant | Tier B; the gate is the data envelope, not the build |

The gate is the **Tier A sponsor data declaration** already defined in [engineering-optional](./engineering-optional.md#data-classification-at-tier-a): no customer PII, no regulated data, no production database access, sanctioned SaaS only. A tool that passes the declaration is a candidate for a BMAD build. A tool that fails it is Tier B regardless of how fast BMAD could produce it — speed does not move the gate.

## Build-time vs. run-time: the handoff

BMAD ends at an artifact. Our governance owns the artifact's life. The handoff is the load-bearing part of this position.

| Phase | Owner | What happens |
|---|---|---|
| Build | One champion, driving BMAD | Brief → working tool. BMAD's personas stand in for the PM/Architect/UX/Dev roles the org won't staff for this tool. |
| Landing | The tool's owner per [ownership model](../governance/ownership-model.md) | Gate = whoever owns the end user. Tool gets a repo under `rcg-digital-product`, a README with its data declaration, and an owning champion. |
| Life | Owning champion, at Tier A's shape | Fixed in business hours, no on-call, heartbeats to `#pm-toolkit-ops`. Changes go through [approval](../governance/approval-model.md) T0/T1. |

The artifact must be scrubbed of BMAD's single-operator residue at landing — absolute paths replaced, BMAD-internal scaffolding either removed or made portable — so that a second maintainer can own it. This is a checklist item at landing, not an afterthought.

## What this does not change

- **The roadmap.** Most roadmap items ship as skills/agents that plug into SaaS — artifact generators, not standalone applications. BMAD's full implementation cycle is overkill for those; a skill in `pm-toolkit` is the right form. BMAD's real value is the **long tail of internal *applications*** that are not on the roadmap at all — small dashboards, utilities, one-off services central engineering will never prioritize. That long tail is the category this document opens; it does not reclassify any existing roadmap entry.
- **Ownership.** The gate still sits at "owner of the end user." BMAD has no concept of this; adopting it does not import one.
- **The envelope.** A self-served, low-priority tool is not an ungoverned one. The data declaration still gates it.

## Worked example

A support lead wants an internal triage-helper that reads a sanctioned Zendesk view and drafts a first-response template into a Slack channel. Central engineering will not staff it — it is one team's convenience tool, low priority.

1. **Eligibility.** Sponsor files the Tier A declaration: reads `Internal`-class support tickets from a sanctioned SaaS, writes drafts to Slack, no customer PII beyond what the support team already sees, no production DB. Passes. Candidate for a BMAD build.
2. **Build.** One champion drives BMAD: the PM persona frames the job, the Architect persona drafts the minimal design, the Dev persona produces the GitHub Actions + API-call tool. Hours, not a staffed project.
3. **Landing.** Tool lands in a repo under `rcg-digital-product`. Absolute paths from BMAD's output are replaced with `{project_root}`-style relatives. README declares data classes and names the owning champion. Per the ownership model, the gate is owned by product-on-behalf-of-support, because product owns the tool's end user.
4. **Life.** Tool heartbeats to `#pm-toolkit-ops`. The support lead's request changes go through T0/T1 review. If the tool later wants to read customer PII or write to a customer surface, its data declaration changes and it triggers a Tier A → Tier B review — at which point it is no longer a BMAD-grade self-serve tool.

## Anti-goals

- **BMAD as our operating model.** We do not adopt BMAD's agent roster as a replacement for [agent-library.md](../governance/agent-library.md), the ownership model, or the spine. It is an engine, not a constitution.
- **BMAD past the data gate.** No BMAD-built tool ships against customer PII, regulated data, or a production database on the strength of how fast it was built. The gate is the envelope.
- **BMAD as a run-time owner.** We do not leave tools running on BMAD's single-operator scaffolding. Landing scrubs it.
- **Reinventing BMAD.** Where a Stage 1–2 tool's value is the *artifact* and not the *agent mechanics* (PRD drafting, story writing, spec decomposition), prefer adopting BMAD's engine over rebuilding its internals. Our differentiation is the rails around the engine, not the engine.

## Risks

- **Tool sprawl.** A fast self-serve build path produces many small tools. Mitigation: every tool still lands under an owner with a README; the fleet-size review in [engineering-optional](./engineering-optional.md#risks-and-the-case-to-leadership) at 5 agent repos applies to BMAD-built tools too.
- **Quality variance.** A one-champion build has one reviewer. Mitigation: T0/T1 peer review at landing is non-negotiable; the data declaration is reviewed by product leadership at intake.
- **Lock-in to BMAD scaffolding.** Tools built on BMAD-specific structure may be hard to maintain without BMAD. Mitigation: the landing scrub targets portability; a tool that cannot be maintained without re-running BMAD has not landed.
- **Version drift.** BMAD ships near-daily; an internal build pinned to an old version diverges. Mitigation: pin BMAD version per build in the tool README; upgrading is a deliberate per-tool act, mirroring the Tier A → Tier B migration stance.

## Open questions

- **Does the landing scrub need to be a documented checklist?** Probably yes once the second BMAD-built tool lands; defer the checklist to first instance per repo convention.
- **Where do BMAD-built tools that are not on the roadmap get tracked?** The roadmap is for PM-facing tools; the internal long tail may need a lightweight register elsewhere. Open until the first such tool exists.
- **Is one champion + one reviewer sufficient governance for a tool other teams depend on?** The ownership model says the gate follows the end user; an internal tool with a non-PM audience may pull the gate outside product. Resolve per tool at landing.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-06-17 | Lance | Initial draft. Positions BMAD as a build-time engine for the Tier A self-serve lane, gated by the data declaration, handed to spine/governance for run-time. |
