# Strategy

Strategy documents describe deliberate choices that shape *what* we build and *how* we distribute it, separate from policy (governance) and execution detail (specs).

A strategy doc differs from a roadmap entry because it answers "given this constraint or this bet, what changes about our approach?" The roadmap lists what we're building. Strategy docs explain the reasoning when a constraint reshapes the list.

## Current documents

1. **[Licensed seats](./licensed-seats.md)** — How the roadmap, distribution model, and governance change when Claude Code seats are scarce and have to be allocated deliberately.
2. **[Engineering-optional delivery](./engineering-optional.md)** — How the roadmap and governance change when engineering's platform investment is treated as an enhancement tier rather than a precondition. Defines a Tier A baseline that ships on substrate product already has, and a Tier B menu engineering can fund when ready.
3. **[Grounding, contract, destination](./grounding-contract-destination.md)** — Why the roadmap is a portfolio of integrated tools rather than "every PM gets a chat seat." The three structural properties (grounded in company data, ships under a contract, ends at a destination) that separate a roadmap tool from a chat session.
4. **[Definition of Ready standardization](./definition-of-ready-standardization.md)** — A June 2026 Confluence audit found ~25 Definitions of Ready across 16 spaces, falsifying the toolchain's "the team's DoR" assumption. Why the DoR-consuming tools (story writer, Rubric Scorer) are the cheapest forcing function for converging on one machine-readable config per team.

## How strategy docs get added

- Open as a PR. Strategy docs are not policy — they don't bind anyone by themselves — but they're meant to be shared, debated, and updated, so they go through the same review surface as governance.
- A strategy doc that becomes settled policy gets promoted into `governance/` or folded into the roadmap. Until then, it lives here as a working position.

## Status

Draft. First entry added 2026-05-26.
