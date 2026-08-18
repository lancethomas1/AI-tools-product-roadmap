# AI Tools for POs & PMs — Product Roadmap

The working artifact behind a roadmap of AI tools that make non-technical Product Owners and Product Managers more efficient — and the governance, strategy, personas, and per-tool specs that surround it.

This is a **documentation-only** repo. There is no code, no build, and nothing to run. The deliverable is the writing. Implementations of the roadmap tools live in separate repos (see [GitHub org structure](./governance/github-org-structure.md)); this repo is the design layer they are built from.

Start with **[ROADMAP.md](./ROADMAP.md)** — the narrative source of truth for what we're building, in what order, and why.

## The mental model

Three ideas are referenced everywhere in this repo. Everything else hangs off them.

1. **Tools are stations on a pipeline, not standalone products.** Every PM feature moves through five lifecycle stages — Drafting → Planning → Execution → Release → Post-release — and each tool sits at one stage, consuming the previous stage's output and producing the next stage's input. The Now/Next/Later horizons say *when* we build; the lifecycle map says *what role each tool plays*.
2. **The spine.** Every Stage 2–5 tool starts from a common reference: the **Confluence PRD** plus the **Jira epic**. When a tool cannot resolve the spine, it fails loudly; it does not reconstruct context. This is the single most-cited principle in the repo.
3. **Ownership is the variable; guardrails and the envelope are constants.** The [ownership model](./governance/ownership-model.md) decides who owns the gate for a given tool based on who owns the end user. The [engineering guardrails](./governance/engineering-guardrails.md) and the [security & data envelope](./governance/security-data-envelope.md) apply no matter who owns it.

## Repository map

| Path | What it holds |
|---|---|
| [ROADMAP.md](./ROADMAP.md) | Narrative source of truth: Now / Next / Later horizons, the feature lifecycle map, the spine, delivery tiers, success metrics. |
| [governance/](./governance/README.md) | How tools get built, shipped, and owned — the rails inside which product self-serves. Ownership, guardrails, approval tiers (T0–T3), the security/data envelope, the reusable agent library, GitHub org structure, and the contributor ladder. |
| [specs/](./specs/) | Per-tool specs that turn a roadmap entry into a buildable design. One file per tool, kebab-cased to match its roadmap name, linked from `ROADMAP.md`. |
| [personas/](./personas/README.md) | The POs/PMs the tools serve, the stakeholder audiences they write for, the voices the agents speak in, and the synthetic personas the eval suites run against. |
| [strategy/](./strategy/README.md) | Working positions on bets and constraints that reshape the roadmap (seat scarcity, engineering-optional delivery, why tools instead of chat). Promoted to `governance/` only when settled. |
| [comms/](./comms/README.md) | Drafts and sent versions of stakeholder messages, so the "why" stays next to the "what". |

## How to navigate common questions

| Question | Where to look |
|---|---|
| Which tool is in which stage or horizon? | [ROADMAP.md](./ROADMAP.md) — lifecycle map and Now/Next/Later sections |
| Who owns this tool? | [governance/ownership-model.md](./governance/ownership-model.md) |
| What review does this change need? | [governance/approval-model.md](./governance/approval-model.md) |
| Can a tool process this data? | [governance/security-data-envelope.md](./governance/security-data-envelope.md) |
| Which agent does X? | [governance/agent-library.md](./governance/agent-library.md) |
| Where would an implementation repo live? | [governance/github-org-structure.md](./governance/github-org-structure.md) |
| Who is this tool for? | [personas/](./personas/README.md), cited from each spec |
| Why a toolset instead of "give every PM a chat seat"? | [strategy/grounding-contract-destination.md](./strategy/grounding-contract-destination.md) |
| Does Claude Code seat scarcity change the roadmap? | [strategy/licensed-seats.md](./strategy/licensed-seats.md) |

## How this repo is maintained

- Everything is Markdown and everything changes by PR off `main`. Doc PRs are small: one logical change per commit.
- `ROADMAP.md` is edited in place, never forked. Changes to horizons, lifecycle mapping, or metrics ripple into specs and governance — downstream references are updated in the same PR or called out explicitly.
- Governance docs carry a Version history table; every substantive edit bumps it.
- Conventions for structure, tone, filenames, and where new content goes are spelled out in [CLAUDE.md](./CLAUDE.md), which doubles as the contributor guide for humans and AI assistants alike.

Everything here is internal. No customer-facing content, no PII, no secrets — per the data classification in the [security & data envelope](./governance/security-data-envelope.md).
