# CLAUDE.md

Guidance for AI assistants (Claude Code in particular) working in this repository.

## What this repo is

A **documentation-only** repo. No code, no build, no tests, no package manager. Every file is Markdown. The deliverable is the writing.

The repo is the working artifact behind a roadmap of AI tools that serve Product Owners and Product Managers, and the governance, strategy, and specs that surround it. See `ROADMAP.md` for the narrative source of truth.

There is nothing to compile and nothing to run. Do not propose adding CI, package files, linters, or scripts unless explicitly asked.

## Repository map

```
ROADMAP.md         Narrative source of truth: Now / Next / Later, lifecycle stages, spine principle.
README.md          One-line landing; do not expand without being asked.

governance/        How tools get built, shipped, and owned. The rails inside which product self-serves.
  README.md
  ownership-model.md          Who owns the gate for a given tool.
  engineering-guardrails.md   Platform standards every tool inherits.
  approval-model.md           Tiered (T0–T3) review model for changes.
  security-data-envelope.md   Non-negotiable data, secrets, AI, and incident rules.
  agent-library.md            Reusable agents (Spine Resolver, PRD Drafter, etc.) and their contracts.
  github-org-structure.md     Where product-owned repos live in GitHub.

specs/             Per-tool specs that turn a roadmap entry into a buildable design.
  story-ticket-writer.md
  backlog-grooming-copilot.md

strategy/          Working positions on bets/constraints that reshape the roadmap.
  README.md
  licensed-seats.md           How scarcity of Claude Code seats changes the roadmap.

comms/             Drafts and sent versions of POV messages.
  README.md
  2026-05-24-product-owned-tooling-heather.md
```

## Core mental model (read before editing)

Three documents are referenced everywhere. Internalize them before changing related content:

1. **Ownership is the variable.** `governance/ownership-model.md` decides who owns the gate per tool, based on who owns the end user.
2. **Guardrails and the envelope are constants.** `governance/engineering-guardrails.md` and `governance/security-data-envelope.md` apply regardless of ownership.
3. **The spine.** Every Stage 2–5 tool starts from the **Confluence PRD + Jira epic**. Tools fail loudly when the spine cannot be resolved; they do not reconstruct context. This is the single most-cited principle in the repo — preserve it in any edit that touches tool behavior.

Read `ROADMAP.md` for the lifecycle stages (Drafting → Planning → Execution → Release → Post-release). Tools are stations on a pipeline, not standalone products.

## Conventions when editing

### Where new content goes

| Kind of content | Directory | Notes |
|---|---|---|
| Roadmap horizon, lifecycle, or operating-principle change | `ROADMAP.md` | Single source of truth. Edit in place; do not fork. |
| New policy that binds owners | `governance/` | Add Version history table; cross-link from `governance/README.md`. |
| Per-tool design | `specs/<tool-name>.md` | Use kebab-case matching the roadmap tool name. Link from `ROADMAP.md`. |
| A working position not yet policy | `strategy/` | Promote to `governance/` only when settled. |
| A stakeholder message draft | `comms/YYYY-MM-DD-short-slug-[recipient].md` | One file per message. Frontmatter block at top (to / channel / status / purpose). |

Do not invent new top-level directories without a reason explicitly grounded in the existing structure (e.g. `governance/decisions/` and `governance/exceptions/` are referenced as planned subdirs — create on first use, not preemptively).

### Filename conventions

- Markdown only; `.md` extension.
- kebab-case (`agent-library.md`, not `AgentLibrary.md` or `agent_library.md`).
- Specs match the roadmap tool name, kebab-cased, no `tool-` prefix.
- Comms files: `YYYY-MM-DD-short-slug-recipient.md`.
- No stage prefixes (`stage1-`, `drafting-`) on spec filenames — lifecycle stage is metadata, not identity (per `governance/github-org-structure.md`).

### Document structure

Governance docs follow a recognizable shape; preserve it when editing:

```
# Title
## Purpose          (why this doc exists, 1–3 paragraphs)
## Principle        (the one sentence the doc is defending)
## Scope            (what it applies to)
## <body sections>
## Roles            (table: role → responsibility)
## Review cadence
## Version history  (table: version | date | author | change)
```

Specs follow the shape established in `specs/story-ticket-writer.md` and `specs/backlog-grooming-copilot.md`:

```
# <Tool> — Spec
- Horizon / Stage / Theme / Owner / Status   (5-line metadata block)

## Why this tool, why now
## What we mean by "<the verb>"     (in-scope / not-in-scope at conceptual level)
## Problem
## Users & jobs-to-be-done
## In scope (v1)  /  Out of scope (v1)
## Capabilities                     (table)
## Integrations
## UX surfaces
## Trust & safety
## Success metrics
## Rollout phasing                  (Alpha / Beta / GA)
## Dependencies & open questions
## <Tool-specific mechanics>
## Evaluation criteria & metrics    (three layers: Output / Behavior / Outcomes + Guardrails + Anti-metrics)
## Failure modes & mitigations      (table)
## Cost & latency envelope
## User-flow walkthroughs           (Flow A / B / C)
## Anti-goals
```

Reuse this shape for new specs unless the tool genuinely doesn't fit. Don't simplify it away — the long shape is the trust bar.

### Cross-references

- Use relative Markdown links: `[approval model](./approval-model.md)`, `[ROADMAP.md](../ROADMAP.md)`.
- When you add a concept that's defined elsewhere, link the first mention.
- When a doc references a planned but not-yet-created path (e.g. `governance/decisions/`), do not create an empty directory. Create the file when the first instance lands.

### Version history

Every governance doc carries a Version history table at the bottom. **Bump the table on every substantive edit.** Non-substantive (typo, link fix) does not need a bump; rewording a definition, adding a section, or changing a rule does.

Author name in the version table is whoever the PR is by. If you're editing as Claude on behalf of a human, attribute the human, not the model.

### Tone and style

The docs are deliberately opinionated and assertive. Match that register:

- **Lead with the principle.** Most sections in this repo start with the single sentence the section is defending. Preserve that pattern.
- **Tables for rules, lists for examples.** Long prose paragraphs are reserved for rationale.
- **Worked examples.** Almost every governance doc has a "Worked examples" or "User-flow walkthroughs" section. New rules should ship with at least one example.
- **Anti-metrics, anti-goals, and failure modes are first-class.** When adding a new capability or rule, ask what it pushes the system to do badly and write it down.
- **No marketing voice.** Plain, declarative, sometimes contrarian. The author's voice is consistent across docs; match it.
- **Don't use emoji** in any file unless explicitly asked.

## What to change vs. propose

This repo is policy and design. Edits have consequences. Calibrate accordingly:

| Change | Default behavior |
|---|---|
| Typo, broken link, formatting fix | Edit directly. |
| Clarifying a definition without changing scope | Edit; bump version history. |
| Adding a worked example to an existing rule | Edit; bump version history. |
| New rule, new tier, new agent contract, new tool entry | Propose first (write up the diff and rationale), then edit on approval. |
| Reclassifying a tool (ownership, horizon, stage) | Propose; this is the kind of change the docs explicitly say happens by deliberate act, not drift. |
| New top-level directory | Propose. The structure is intentional. |

When in doubt, smaller edit + bump history > larger edit without confirmation.

## Branch and commit conventions

- All work happens on feature branches off `main`. Do not push to `main` directly.
- Branch names follow `claude/<short-slug>-<suffix>` for AI-assisted branches (see existing branches like `claude/claude-md-docs-cXapS`).
- Commit messages are short, imperative, and describe the substantive change (e.g. `Add agent library doc defining reusable agents that compose into roadmap tools`). Match the style in `git log`.
- One logical change per commit. Doc PRs are usually small; resist sweeping refactors across multiple docs in one commit.

## Things this repo does *not* do

To avoid wasted effort or scope creep:

- **No implementation code.** This repo is the design layer. Implementations of the roadmap tools live in separate repos under `rcg-digital-product/*` (see `governance/github-org-structure.md`). Do not add `src/` or example code here.
- **No CI, no lint, no tests, no `package.json`, no `requirements.txt`.** Markdown-only. Spelling and formatting are reviewed by humans in PR.
- **No customer-facing content.** Every document here is internal. If a doc starts to read like marketing copy, redirect.
- **No PII, no real customer data, no secrets.** This repo is `Internal` per the data classification in `governance/security-data-envelope.md`.

## Quick references for common questions

- **"Which tool is in which stage / horizon?"** → `ROADMAP.md`, the Feature lifecycle map and Now/Next/Later sections.
- **"Who owns this tool?"** → Apply `governance/ownership-model.md` decision criteria.
- **"What review does this change need?"** → `governance/approval-model.md`, T0–T3 tiers.
- **"Can the tool process this data?"** → `governance/security-data-envelope.md`, data classification table.
- **"Which agent does X?"** → `governance/agent-library.md`, the at-a-glance table.
- **"Where would this repo live?"** → `governance/github-org-structure.md`.
- **"Does seat scarcity change the roadmap?"** → `strategy/licensed-seats.md`.

## When the human asks you to write a new doc

1. Confirm the directory based on the table in **Where new content goes** above.
2. Reuse the closest existing template in that directory (don't invent a new structure).
3. Link the new doc from the directory's `README.md` (or `ROADMAP.md` for specs).
4. If it's a governance doc, include the Version history table from day one.
5. Cross-link to every adjacent doc the reader will need.
6. Keep the principle-first / table-heavy / worked-example style.

## When the human asks you to update the roadmap

`ROADMAP.md` is the narrative source of truth. Changes to horizons, themes, lifecycle mapping, or success metrics ripple into specs and governance — check for downstream references (`grep -r "Now horizon"`, etc.) and update them in the same PR, or call out follow-ups explicitly.
