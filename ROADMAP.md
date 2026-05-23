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
6. **Lifecycle, not feature type.** Tools are stations on the feature delivery pipeline, not standalone products. Output from one tool is input to the next.

---

## Feature lifecycle map

Every PM feature moves through 5 stages. Each tool sits at one stage, consuming output from the prior stage and producing input for the next. This is the primary organizing principle — the Now/Next/Later horizons below describe *when* we build, not *what role each tool plays*.

| Stage | What the PM is doing | Tools |
|---|---|---|
| **1. Drafting** | New idea → first PRD | PRD drafting assistant |
| **2. Planning** | PRD → tickets → sprint plan | Story & ticket writer · Spec → sprint decomposer |
| **3. Execution** | Run the sprint, keep the backlog clean | Backlog grooming copilot · Weekly status synthesizer · Meeting → artifact pipeline · Proactive sprint agent |
| **4. Release** | Ship + tell people | Release notes generator · Stakeholder comms tailoring · Cross-functional launch checklist |
| **5. Post-release** | Feedback + memory | PM knowledge agent · Living spec sync |

### The shared spine

Tools across stages need a common reference to the feature they're working on. The spine is:

- **Confluence PRD** (the narrative source of truth, produced in Stage 1)
- **Jira epic** (the execution surface, created at Stage 2, references the PRD)

Downstream tools (Stages 2-5) MUST start from the spine. A ticket writer reads the PRD's in-scope list. A release notes generator reads PRs merged against the epic. A status synthesizer reads tickets under the epic. If a tool can't locate the spine, it should fail loudly rather than reconstruct context.

This has a direct implication for Stage 1: **the PRD assistant's output must be structured for downstream consumption.** Explicit in-scope list, user-facing impact bullets, named stakeholders. We measure the PRD assistant in part by how cleanly Stages 2-4 tools can consume its output.

### Greenfield vs. in-flight

Most tools work for both modes, but Stage 1 (PRD drafting) is explicitly **greenfield-only** in v1 — the assistant drafts new PRDs, not updates to existing ones. Mid-flight PRD updates are covered by Living Spec Sync (Stage 5, Later).

---

## Now

Build/adopt this quarter. Highest value-to-effort, unlocks daily-use habits.

- **PRD drafting assistant** *(Stage 1 — Drafting)* — Generate a first-draft PRD from a one-paragraph brief using the team's template. Suggests problem framing, success metrics, and open questions to resolve. Output structured for downstream consumption.
- **Story & ticket writer** *(Stage 2 — Planning)* — Convert PM bullet points or a PRD section into properly formatted user stories with acceptance criteria, sized for the team's Definition of Ready. Reads from the spine PRD.
- **Release notes generator** *(Stage 4 — Release)* — Synthesize merged PRs and shipped tickets under a Jira epic into customer-facing release notes, internal changelog, and a Slack announcement variant.
- **Weekly status synthesizer** *(Stage 3 — Execution)* — Pull from Linear/Jira, GitHub, and Slack threads tied to active epics to draft the PM's weekly team and exec updates. PM edits, then sends.

## Next

Plan for 1–2 quarters out. Higher value but requires more integration and trust-building.

- **Backlog grooming copilot** *(Stage 3 — Execution)* — Flags duplicates, suggests merges, surfaces stale items, and proposes priority changes based on linked goals or customer signal.
- **Stakeholder comms tailoring** *(Stage 4 — Release)* — Turn one PM-written update into audience-tuned variants (exec, sales, eng, CS) with appropriate framing and detail level.
- **Spec → sprint decomposer** *(Stage 2 — Planning)* — Take a PRD and propose a sprint-ready ticket breakdown with estimates, dependencies, and a draft milestone plan for engineering review.
- **Meeting → artifact pipeline** *(Stage 3 — Execution)* — Turn sync notes into action items, ticket drafts, or PRD updates with attribution back to who said what.

## Later

Exploratory. Bigger bets, ambient ROI, dependent on earlier tools building trust and data.

- **PM knowledge agent** *(Stage 5 — Post-release)* — RAG over all PRDs, decision logs, retros, and Slack to answer "why did we decide X?" or "what did we try last time?" with citations.
- **Living spec sync** *(Stage 5 — Post-release)* — Keep PRDs and ticket descriptions in sync as either changes; flag drift between intent and implementation. Covers the in-flight PRD-update gap.
- **Cross-functional launch checklist agent** *(Stage 4 — Release)* — Auto-generates ready-for-launch checklists per feature, tracks cross-functional dependencies (legal, marketing, support), nags owners.
- **Proactive sprint agent** *(Stage 3 — Execution)* — Watches sprint progress and proposes interventions (re-scope, re-prioritize, flag risk to PM) before standup.

---

## What's not on this roadmap (and why)

- **Discovery & research tools** (user interview synthesis, competitive analysis) — out of scope for this horizon; revisit after delivery/docs tools land.
- **Roadmapping & prioritization AI** — strategic judgment work; we're not ready to delegate it and the trust bar is high.
- **Analytics copilots** — owned by the data team's tooling roadmap; we'll integrate, not duplicate.
- **Standalone PM AI app** — we don't believe PMs need another surface. Everything ships into tools they already use.

## How this roadmap is maintained

- Each item is a GitHub Issue labeled `roadmap`, a horizon label (`now` / `next` / `later`), a theme label (`writing-docs` / `backlog-delivery`), and a stage label (`stage:drafting` / `stage:planning` / `stage:execution` / `stage:release` / `stage:post-release`).
- This document is the narrative source of truth — update via PR when horizons, themes, or lifecycle mapping shifts.
- Day-to-day work is driven through Claude Code, not the GitHub UI. Issues are the system of record; the conversation is the workbench.
