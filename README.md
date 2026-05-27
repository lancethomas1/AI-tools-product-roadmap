# AI Tools for POs & PMs — Roadmap

Internal design layer for the AI tools we're building to make non-technical Product Owners and Product Managers more efficient. Scope is intentionally narrow: **Writing & Docs** and **Backlog & Delivery**. Discovery, research, and strategy tools are out of scope for this horizon.

This repository is documentation only. The narrative roadmap, the governance that surrounds it, and the per-tool specs all live here. Implementations of the tools live in separate repos under the `rcg-digital-product/*` org (see [github-org-structure.md](./governance/github-org-structure.md)).

## Start here

- **[ROADMAP.md](./ROADMAP.md)** — narrative source of truth. Goals, success metrics, operating principles, lifecycle stages, and the Now / Next / Later horizons.
- **[CLAUDE.md](./CLAUDE.md)** — conventions for editing this repo (filenames, document shapes, version history, tone).

## Core mental model

Three ideas reappear across every doc. Internalize them before editing related content.

1. **The spine.** Every Stage 2–5 tool starts from the **Confluence PRD + Jira epic**. Tools fail loudly when the spine cannot be resolved; they do not reconstruct context. This is the single most-cited principle in the repo.
2. **Ownership is the variable.** [`governance/ownership-model.md`](./governance/ownership-model.md) decides who owns the gate per tool, based on who owns the end user.
3. **Guardrails and the envelope are constants.** [`governance/engineering-guardrails.md`](./governance/engineering-guardrails.md) and [`governance/security-data-envelope.md`](./governance/security-data-envelope.md) apply regardless of ownership or tier.

Tools are stations on a five-stage pipeline (Drafting → Planning → Execution → Release → Post-release), not standalone products. Output from one tool is input to the next.

## Repository map

| Directory | What lives here |
|---|---|
| [`ROADMAP.md`](./ROADMAP.md) | Narrative source of truth: horizons, lifecycle map, spine principle, success metrics. |
| [`governance/`](./governance/README.md) | How tools get built, shipped, and owned. Ownership model, approval tiers, engineering guardrails, security envelope, reusable agent library, GitHub org structure. |
| [`specs/`](./specs/) | Per-tool specs that turn a roadmap entry into a buildable design. Currently: [story-ticket-writer](./specs/story-ticket-writer.md), [backlog-grooming-copilot](./specs/backlog-grooming-copilot.md). |
| [`strategy/`](./strategy/README.md) | Working positions on bets and constraints that reshape the roadmap. Promote to `governance/` when settled. |
| [`personas/`](./personas/README.md) | PM archetypes the tools serve, stakeholder audiences they write for, agent voices, and evaluation personas. Every spec cites these. |
| [`comms/`](./comms/README.md) | Drafts and sent versions of POV messages to stakeholders. |

## What we're building

The full Now / Next / Later list with stage and tier annotations is in [ROADMAP.md](./ROADMAP.md). Headlines:

- **Now** — PRD drafting assistant, story & ticket writer, release notes generator, weekly status synthesizer.
- **Next** — Backlog grooming copilot, stakeholder comms tailoring, spec → sprint decomposer, meeting → artifact pipeline.
- **Later** — PM knowledge agent, living spec sync, cross-functional launch checklist, proactive sprint agent.

Each item is labelled **Tier A** (ships on substrate we already have — GitHub, Claude Code, existing SaaS) or **Tier B** (requires engineering platform investment not yet committed). Rationale: [`strategy/engineering-optional.md`](./strategy/engineering-optional.md).

## What this repo is not

- **Not implementation code.** No `src/`, no build, no tests, no package manifest. The deliverable is the writing.
- **Not customer-facing.** Every document here is internal. Classification is `Internal` per [`security-data-envelope.md`](./governance/security-data-envelope.md).
- **Not a roadmap of features in flight.** Day-to-day execution lives in GitHub Issues and the implementation repos. This repo holds the design and the policy.

## Contributing

- Work on a feature branch off `main`. Do not push to `main` directly.
- For AI-assisted branches, use `claude/<short-slug>-<suffix>`.
- One logical change per commit. Imperative, declarative commit messages — match the style in `git log`.
- For governance docs, bump the **Version history** table on substantive edits.
- See [CLAUDE.md](./CLAUDE.md) for the full conventions, including where new content goes, filename rules, document shapes, and when to propose vs. edit directly.
