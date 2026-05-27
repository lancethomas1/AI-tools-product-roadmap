# Personas

The people the tools in [ROADMAP.md](../ROADMAP.md) serve, the audiences those tools write for, the voices the agents speak in, and the synthetic personas the evals run against.

## Why this directory exists

Every spec has a "Users & jobs-to-be-done" section. Every audience-tailoring tool has a list of variants. Every eval suite has implicit assumptions about what kind of PM input is realistic. Today those definitions live duplicated across specs and agent contracts, drifting whenever a new tool gets written. This directory makes them a single, citable source.

## Principle

**Personas describe who, not how.** A persona file says what a person is trying to do and how they will react when a tool gets it wrong. It does not prescribe UX, copy, or model behavior — that is the spec's job. When a spec needs to make a tradeoff, it cites a persona; when a persona changes, every spec citing it gets re-examined.

A persona earns a file when at least two specs reference it, or when it represents an audience or input class the tools must explicitly handle. The directory is intentionally small.

## Scope

| Sub-set | What it defines | Where it's used |
|---|---|---|
| **User personas** | The POs and PMs the roadmap tools serve. Real people with real jobs, drawn as archetypes. | Every spec's "Users & jobs-to-be-done" section cites one or more. |
| **Stakeholder personas** | The audiences PMs write for (exec, sales, eng, CS, support). | Audience Tailor agent · Stakeholder comms tailoring · Release notes generator. |
| **Agent voices** | The voice and tone profile each agent in the [agent library](../governance/agent-library.md) writes in. | All agents producing PM-visible text. |
| **Evaluation personas** | Synthetic inputs the eval suites run against — happy-path and adversarial. | Per-agent eval suites, per-spec acceptance tests. |

## The index

### User personas (POs and PMs)

| File | Archetype | Tenure | Team type | Primary lifecycle stages |
|---|---|---|---|---|
| [maya-senior-growth-pm.md](./maya-senior-growth-pm.md) | Senior Growth PM | 6–8 yrs | Growth, customer-facing | 1, 3, 4 |
| [devon-platform-po.md](./devon-platform-po.md) | Platform PO | 4–6 yrs | Platform / infrastructure | 2, 3, 5 |
| [priya-new-pm.md](./priya-new-pm.md) | New PM | 0–2 yrs | Single feature squad | 1, 2 |
| [sam-staff-multi-team-pm.md](./sam-staff-multi-team-pm.md) | Staff Multi-Team PM | 8+ yrs | Multiple epics / squads | 3, 4, 5 |

### Other persona sets

| File | What it covers |
|---|---|
| [stakeholders.md](./stakeholders.md) | Executive, Sales, Engineering, Customer Success, Support — the five audiences PM artifacts get tailored for. |
| [agent-voices.md](./agent-voices.md) | The voice profile for each agent family in the library (Drafter, Synthesizer, Auditor, Tailor). |
| [evaluation-personas.md](./evaluation-personas.md) | Synthetic happy-path and adversarial inputs the eval suites run against. |

## How to use these

| When you are… | Do this |
|---|---|
| Writing a new spec | Cite at least one user persona in "Users & jobs-to-be-done". Check the spec against each persona's frustrations and failure modes. Don't paste persona content into the spec — link it. |
| Defining a stakeholder-tailored variant | Cite the relevant entry in `stakeholders.md` for tone, length, and risk-preservation rules. |
| Designing or changing an agent's output | Cite the relevant entry in `agent-voices.md`. Voice changes that cross profiles (e.g., Auditor → Drafter) need their own PR, not a quiet drift. |
| Writing or extending an eval suite | Pull inputs from `evaluation-personas.md` instead of inventing one-off test cases. Adversarial inputs are the trust bar — happy-path inputs alone are not. |
| Adding a new persona | Confirm it earns its place (two specs reference it, or it represents a distinct audience/input class). Use the closest existing persona as a template. Bump version history on the new file from day one. |

## What this directory does not do

- **Doesn't define tools.** Tools live in `specs/`.
- **Doesn't bind policy.** Persona files inform; they don't approve. If a persona's needs and a guardrail conflict, the guardrail wins.
- **Doesn't replace user research.** These are archetypes, not interview transcripts. When real interview data lands, update the persona, do not fork.
- **Doesn't carry real names or PII.** All persona first-names here are archetype labels. Do not edit a persona to match a specific real PM in the org.

## Conventions

- **Filenames:** kebab-case. User personas use `<firstname>-<role-slug>.md` (e.g., `maya-senior-growth-pm.md`). Non-user persona sets use a noun slug (`stakeholders.md`, `agent-voices.md`).
- **Shape:** see the template in the User-persona files. Stakeholder/voice/eval sets follow the shape inside each of those files; reuse, don't reinvent.
- **Cross-links:** every persona links the specs and agents it informs. Every spec citing a persona links back.
- **No emoji.** Match the repo style.

## Roles

| Role | Responsibility |
|---|---|
| **Persona owner** (per file) | Maintains the file. Re-checks the archetype against new user data quarterly. Named in the version history. |
| **Spec author** | Cites the relevant personas in every new spec. Reports persona friction back via PR comment on the persona file. |
| **Tool owner** | Re-reads the cited personas before a tool's eval refresh. Flags drift if real-user behavior diverges from the archetype. |

## Review cadence

- User personas reviewed every quarter, or when a new user-research signal warrants. The intent is durability — frequent persona churn means the archetypes are wrong.
- Stakeholder personas reviewed when a new audience surfaces (e.g., adding "Field marketing" as a distinct stakeholder) or when a stakeholder's surface changes substantially.
- Agent voices reviewed alongside the [agent library](../governance/agent-library.md) major version bumps.
- Evaluation personas extended whenever a new failure mode is observed in production.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial directory. README, four user personas, stakeholder set, agent voice set, evaluation persona set. |
