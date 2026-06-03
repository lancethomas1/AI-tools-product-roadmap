# Definition of Ready Standardization: The Tools as the Forcing Function

How the roadmap changes once you accept that there is no single "Definition of Ready" to configure against — and why the tools that *consume* a DoR are the most credible mechanism we have to converge on one.

## Purpose

Three of the [roadmap](../ROADMAP.md)'s tools and four of its [reusable agents](../governance/agent-library.md) are built on a quiet assumption: that each team has *one* Definition of Ready, expressible as a `dor_config` the [Story & ticket writer](../specs/story-ticket-writer.md) and [Rubric Scorer](../governance/agent-library.md#11-rubric-scorer) can score against. The [ROADMAP](../ROADMAP.md) says the story writer produces tickets "sized for the team's Definition of Ready" — singular, definite article.

A Confluence audit run in June 2026 falsified that assumption. There is no "the" DoR. There are at least **25 standalone DoR pages across 16 spaces**, plus a long tail embedded in process docs — and the one space that looks like it's trying to be canonical contains **four distinct DoR artifacts in three incompatible formats, none cross-linked**.

This document records the finding, names the risk it creates for the toolchain, and argues that the right response is not "wait for the org to standardize, then build" but "build the tools such that adopting them *is* the standardization." It is a working position, not policy.

## Principle

**A Definition of Ready that lives only as prose on a wiki page cannot be enforced, so it forks; a Definition of Ready that lives as a machine-readable config a tool scores against has exactly one source of truth per team, because the tool refuses to run without one.** The tools don't wait for standardization — they are the cheapest path to it.

## The evidence

A two-pass Confluence search (`text ~ "definition of ready"` and the `DoR` abbreviation), then a hierarchy walk of the most-canonical space:

| Finding | Detail |
|---|---|
| Standalone DoR pages | **~25**, a floor — site search caps results and the `DoR` token is noisy |
| Spaces involved | **16** (NCPHYB, EX, CHEC, MEGA, DRE, DD, DICT, VC, SSH, FAD, CD, DBS, PAYM, DP, MDM, CE) |
| Known duplicate forks | DICT and VC pages are identical in their indexed body; CHEC and MEGA share an "Excalibur" lineage |
| State of the pages | Several explicitly `WIP` / `DRAFT` / "to be discussed"; one is a pasted image; one is an empty shell pointing at a PDF; one has a typo in the title |
| Embedded-in-process versions | ~5 more inside Agile Definitions, Community Working Agreement, PI Planning guidelines, the PMO Framework |

The state *inside* the Digital Delivery (`DD`) space — the one that reads as an attempt at a canonical home — is the sharpest illustration:

| Page | Version | Format | What it is |
|---|---|---|---|
| Definition of Ready & Done | **v15** | Structured criteria + RACI | The mature, de-facto canonical one. Feature- and story-level DoR, RACI table, two slide decks attached. |
| Zero tolerance DoR/DoD | v1 | Prose policy essay | A philosophy doc (INVEST, max story size, "Done means Done"). No checklist. |
| DOR checklist for a story | v1 | Checkbox checklist | Sections A–E. Overlaps the v15 page's story level with *different* criteria. |
| DOR Checklist for Defect | v1 | Checkbox checklist | A defect-specific fourth flavor. |

The three v1 pages are **orphaned** — they don't hang off the mature v15 page, and the [PMO Framework](https://confluence.rccl.com/pages/viewpage.action?pageId=437919894) that is supposed to govern DoR has 22 child pages, none of which is *any* DoR. So even the consolidation attempt is re-fragmenting.

## Why this matters to the toolchain specifically

The DoR sprawl isn't just an org-hygiene problem someone else owns. It is a direct, unaddressed input risk to tools already on the Now/Next list. Everywhere the toolchain says "the team's DoR," it is pointing at one of ~25 documents and silently assuming the PM knows which:

| Tool / agent | DoR dependency | What sprawl does to it |
|---|---|---|
| [Story & ticket writer](../specs/story-ticket-writer.md) (Now) | Scores every draft against `dor_config`; blocks one-click commit on DoR-fail | Which of the 25 is `dor_config`? A wrong choice means the tool enforces a DoR the team doesn't actually use — and does it with machine authority. |
| [Rubric Scorer](../governance/agent-library.md#11-rubric-scorer) agent | Generic checklist scorer; DoR is its headline config | A scorer is only as trustworthy as its rubric's provenance. Unsourced rubric = confident scoring of the wrong thing. |
| [Spec → sprint decomposer](../specs/spec-sprint-decomposer.md) (Next) | "Stories ready-at-planning (DoR-pass) >85%" | The ground-truth metric is undefined if "ready" isn't pinned. |
| [PRD drafting assistant](../specs/prd-drafting-assistant.md) (Now) | DoR-style readiness check before "ready for review" | Inherits whichever DoR the upstream config names. |
| [Incoming defect triage copilot](../specs/incoming-defect-triage-copilot.md) (Next) | Hands AC stubs to the story writer for "full DoR refinement" | A *defect* DoR exists in DD — so triage and story refinement may need different rubrics, which the current single-config model doesn't express. |

The [Priya persona](../personas/priya-new-pm.md) makes the stakes concrete: a new PM's stated win is *"write the stories that pass our DoR — I don't know all our DoR rules by heart yet."* If the tool points her at the wrong DoR, it doesn't just fail to help — it teaches her a standard her team will reject at refinement, with the tool's authority behind it. The spec already names this failure: a DoR check that "passes a story that shouldn't have passed."

The story writer's own spec already half-saw this. Its open question — *"DoR config at team or project level? Project adds flexibility, multiplies maintenance."* — is the sprawl problem in miniature, scoped down to one tool. This document zooms it back out: the answer to "team or project" is moot if neither level has a single agreed DoR to begin with.

## The opportunity: tools as the forcing function

The instinct is to treat standardization as a precondition — "we can't build the DoR check until the org agrees on one DoR." That gets the causality backwards. The org has had a wiki for years and produced 25 DoRs; prose does not converge. A scored config does, for a mechanical reason:

- **A prose DoR has no cost to fork.** Copy the page, edit it, ship it. Nobody's blocked. So everybody forks.
- **A `dor_config` the story writer scores against has exactly one binding instance per team**, because the tool reads one config and blocks commits against it. A second config isn't a harmless copy — it's a contradiction the team has to resolve to use the tool at all.

This mirrors the roadmap's own [spine principle](../ROADMAP.md#the-shared-spine): downstream tools *fail loudly* when the spine can't be resolved rather than reconstructing context. The same discipline applies here. **A DoR-consuming tool should resolve exactly one canonical DoR or fail loudly — never silently pick one of 25.** That refusal is the forcing function. The first time the story writer says "no DoR config found for this team — point me at one," the team has to do the thing the wiki never made them do: choose.

Concretely, the standardization path rides on tools already being built, not new work:

1. **`dor_config` becomes the canonical artifact, not a copy of one.** The team's DoR lives as the machine-readable config the [Story Formatter](../governance/agent-library.md#3-story-formatter) and [Rubric Scorer](../governance/agent-library.md#11-rubric-scorer) read — versioned in the toolkit repo, not re-typed on a wiki. The Confluence page, if one is kept, is *generated from* the config, never the reverse.
2. **The mature DD page (v15) seeds the first config.** It is the only DoR in the audit with sustained maintenance and both feature- and story-level structure. Encode it as the reference `dor_config`; the three orphaned v1 DD pages collapse into it (the defect checklist becomes a `dor_config` variant keyed to work-item type, which the triage copilot needs anyway).
3. **The tool's refusal-to-run does the political work.** Each team adopting the story writer must name its one config. Teams with a real local divergence express it as a diff against the reference config — visible, reviewable, finite — instead of a 26th orphaned page.

The leverage: we are not asking 15 spaces to agree out of goodwill. We are making "have one resolvable DoR" the entry cost of a tool PMs already want, and making every remaining divergence a reviewed diff instead of an invisible fork.

## What changes in the roadmap

- **The story writer spec's open question gets an answer.** "Team or project level" resolves to: **one config per team, in the toolkit repo, with typed work-item variants (story / defect / spike) rather than parallel documents.** This should land as an edit to [specs/story-ticket-writer.md](../specs/story-ticket-writer.md) when this position is adopted.
- **A `dor_config` provenance rule.** A tool may only score against a config with a named owner and a single source location — the [Rubric Scorer](../governance/agent-library.md#11-rubric-scorer)'s contract gains a "rubric must cite its source" requirement, the same cite-or-refuse discipline the rest of the roadmap uses.
- **The reference DoR is a shared asset, not per-tool.** Because Story Formatter, the decomposer, the PRD assistant, and the defect triage copilot all consume it, the config belongs alongside the agent library, owned per the [ownership model](../governance/ownership-model.md), not duplicated in each spec.

## Anti-goals

- **Not "the tools define the DoR."** The team defines it; the tool *holds the one copy* and enforces it. We are standardizing the *number* of DoRs (one per team) and their *form* (machine-readable, sourced), not dictating their *content*. A tool that invents DoR criteria the team never agreed to is the [Priya failure mode](../personas/priya-new-pm.md), not the goal.
- **Not a migration project to delete 24 wiki pages.** That's downstream cleanup the owning teams do once a config exists. Boiling the ocean of Confluence is out of scope; seeding one credible config and letting tool adoption pull teams toward it is in scope.
- **Not a claim the audit is exhaustive.** ~25 is a floor. The position doesn't depend on the exact count — it depends only on the count being "many," which is not in doubt.

## Open questions

- **Where does the canonical `dor_config` physically live?** Most likely the `pm-toolkit` repo (see [licensed-seats](./licensed-seats.md)), versioned beside the skills that read it. Confirm against the [GitHub org structure](../governance/github-org-structure.md) thresholds.
- **One reference config, or a small family?** Commerce/App/Next-Cruise programs may have genuinely different readiness bars. The bet is "one reference + reviewed diffs," but the threshold at which a diff becomes its own config needs a number.
- **Who owns the reference config?** This is a [ownership-model](../governance/ownership-model.md) question — the DD space's apparent stewardship of the v15 page is the obvious starting owner, but ownership of the *config* (not the page) needs to be named.
- **What about Definition of Done?** The audit found DoD sprawl tangled with DoR (the v15 page is "Ready & Done"; the "Zero tolerance" doc covers both). DoD is consumed by the [cross-functional launch checklist](../specs/cross-functional-launch-checklist.md) and [handoff runbook generator](../specs/handoff-runbook-generator.md) the same way DoR is consumed by the story writer. The same forcing-function logic likely applies — out of scope here, flagged as a parallel.

## Next steps

If this position holds up to review:

1. Encode the DD v15 "Definition of Ready & Done" page as the reference `dor_config`, with typed work-item variants.
2. Edit [specs/story-ticket-writer.md](../specs/story-ticket-writer.md) to resolve its "team or project level" open question per the answer above.
3. Add the "rubric must cite its source" requirement to the [Rubric Scorer agent contract](../governance/agent-library.md#11-rubric-scorer).
4. Name a config owner via the [ownership model](../governance/ownership-model.md).
5. Open the parallel question for Definition of Done.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-06-03 | Lance | Initial draft from the June 2026 Confluence DoR audit. |
