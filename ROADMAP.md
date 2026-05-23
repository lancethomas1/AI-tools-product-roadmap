# AI Tools for POs & PMs — Roadmap

Internal roadmap for AI tools that make non-technical Product Owners and Product Managers more efficient. Scope is intentionally narrow: **Writing & Docs** and **Backlog & Delivery**. Discovery, research, and strategy tools are out of scope for this horizon.

## Goals

- **Cut time-to-first-draft** for the writing artifacts PMs produce every week (PRDs, release notes, status updates, stakeholder comms).
- **Reduce backlog grooming overhead** so PMs spend planning time on judgment, not formatting.
- **Meet PMs where they work** — integrations into Linear/Jira, Notion/Confluence, and Slack beat a new standalone app.
- **Keep humans in the loop** — every AI-generated artifact is editable and reviewed before it leaves the team.

## Success metrics

| Metric | Target |
|---|---|
| Time from "I need a PRD" to first reviewable draft | -60% |
| Weekly hours per PM spent on status updates and release notes | -50% |
| Stale/duplicate tickets in backlog (rolling 30-day) | -40% |
| Tool adoption (weekly active PM users) | >70% within 1 quarter of GA |

## Operating principles

1. **Human-in-the-loop by default.** Nothing posts to customer surfaces, Jira/Linear, or Slack channels without explicit PM review.
2. **Edit-don't-replace.** All output lands as a draft the PM revises. We measure edit distance as a quality proxy.
3. **Citations over confidence.** Anything synthesizing internal context (tickets, docs, Slack) cites sources.
4. **Privacy floor.** PII scrubbing on inputs, no training on internal data, per-tool data residency review.
5. **Integrate, don't aggregate.** Ship as plugins/extensions to existing tools before considering a standalone surface.

---

## Now

Build/adopt this quarter. Highest value-to-effort, unlocks daily-use habits.

- **PRD drafting assistant** — Generate a first-draft PRD from a one-paragraph brief using the team's template. Suggests problem framing, success metrics, and open questions to resolve.
- **Story & ticket writer** — Convert PM bullet points or a PRD section into properly formatted user stories with acceptance criteria, sized for the team's Definition of Ready.
- **Release notes generator** — Synthesize merged PRs and shipped tickets in a date range into customer-facing release notes, internal changelog, and a Slack announcement variant.
- **Weekly status synthesizer** — Pull from Linear/Jira, GitHub, and Slack threads to draft the PM's weekly team and exec updates. PM edits, then sends.

## Next

Plan for 1–2 quarters out. Higher value but requires more integration and trust-building.

- **Backlog grooming copilot** — Flags duplicates, suggests merges, surfaces stale items, and proposes priority changes based on linked goals or customer signal. ([spec](specs/backlog-grooming-copilot.md))
- **Stakeholder comms tailoring** — Turn one PM-written update into audience-tuned variants (exec, sales, eng, CS) with appropriate framing and detail level.
- **Spec → sprint decomposer** — Take a PRD and propose a sprint-ready ticket breakdown with estimates, dependencies, and a draft milestone plan for engineering review.
- **Meeting → artifact pipeline** — Turn sync notes into action items, ticket drafts, or PRD updates with attribution back to who said what.

## Later

Exploratory. Bigger bets, ambient ROI, dependent on earlier tools building trust and data.

- **PM knowledge agent** — RAG over all PRDs, decision logs, retros, and Slack to answer "why did we decide X?" or "what did we try last time?" with citations.
- **Living spec sync** — Keep PRDs and ticket descriptions in sync as either changes; flag drift between intent and implementation.
- **Cross-functional launch checklist agent** — Auto-generates ready-for-launch checklists per feature, tracks cross-functional dependencies (legal, marketing, support), nags owners.
- **Proactive sprint agent** — Watches sprint progress and proposes interventions (re-scope, re-prioritize, flag risk to PM) before standup.

---

## What's not on this roadmap (and why)

- **Discovery & research tools** (user interview synthesis, competitive analysis) — out of scope for this horizon; revisit after delivery/docs tools land.
- **Roadmapping & prioritization AI** — strategic judgment work; we're not ready to delegate it and the trust bar is high.
- **Analytics copilots** — owned by the data team's tooling roadmap; we'll integrate, not duplicate.
- **Standalone PM AI app** — we don't believe PMs need another surface. Everything ships into tools they already use.

## How this roadmap is maintained

- Each item is a GitHub Issue labeled `roadmap` plus a horizon label (`now` / `next` / `later`) and a theme label (`writing-docs` / `backlog-delivery`).
- A GitHub Project board (Now / Next / Later columns) is the live view.
- This document is the narrative source of truth — update via PR when horizons or themes shift.
