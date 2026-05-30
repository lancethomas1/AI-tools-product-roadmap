# Handoff runbook generator — Spec

- **Horizon:** Later
- **Stage:** 4 — Release
- **Theme:** backlog-delivery
- **Owner:** TBD
- **Status:** Draft

## Why this tool, why now

A feature's hardest transition isn't shipping — it's the moment it stops being *built* and starts being *run*. The squad that built it moves on; the [RTB PO](../personas/jordan-rtb-po.md) inherits it. That transition is an **ownership transfer**, and ownership is the variable this whole repo turns on (see [`governance/ownership-model.md`](../governance/ownership-model.md)). Today the transfer happens with no contract: the knowledge needed to operate the feature — how it works, what breaks, how to diagnose it, what the SLAs are, who to page, what was deliberately left out — lives in the builder's head and scattered across the PRD, merged PRs, dashboards, and a handful of Slack threads. RTB inherits it cold, then rediscovers it one incident at a time.

The other three RTB tools help Jordan *operate* a feature he already owns. None of them addresses the moment a feature *becomes* his — and that moment is where RTB's debt is created. This tool produces the artifact that makes the transfer honest: a **runbook**, generated at release from the spine plus the implementation, that lets someone who didn't build the feature run it.

It is uniquely positioned among the RTB tools:

- **It is the one spine-rich RTB tool.** The triage copilot, SLA sentinel, and known-issue responder are deliberately spine-*less* — they key off the origin record because Jordan's queue has no PRD+epic. This tool is the opposite: it *consumes* the full spine ([Confluence PRD + Jira epic](../ROADMAP.md#the-shared-spine)) plus merged PRs and incident history, and *produces* the operational layer. It is the bridge from the feature PM's spine world to Jordan's origin-keyed run world: **spine in, operations out.**
- **It bootstraps the rest of the RTB toolset.** A good handoff runbook seeds the [Known-issue responder](./known-issue-responder.md)'s "common issues," sets the defect SLA policy the [SLA / aging sentinel](./sla-aging-sentinel.md) watches, and gives the [Incoming defect triage copilot](./incoming-defect-triage-copilot.md) its initial cluster and failure-mode priors. Without it, those tools start cold on every newly-handed feature.

We sequence it **Later** because it is a required item on, and gates through, the [Cross-functional launch checklist](./cross-functional-launch-checklist.md) (also Later) — a gate item should not ship before the gate that hosts it — and because the highest-value version draws on incident-history depth that matures over time.

## What we mean by "handoff runbook"

A **handoff runbook is the operating manual for a feature, written for someone who didn't build it** — not a design doc, not release notes, not the launch checklist itself.

**In our definition, the runbook covers:**
- **Overview** — what the feature does and who uses it (from the PRD).
- **Operational architecture** — how it works at the level needed to diagnose it: services, dependencies, data flows, third-party calls.
- **Config, flags, and kill-switches** — what's tunable, what the safe defaults are, how to turn it off.
- **Dashboards & SLOs** — where health is observed and what "healthy" looks like.
- **Known limitations & deliberate out-of-scope** — what it intentionally doesn't do (from the PRD's out-of-scope + decisions), so RTB doesn't "fix" a deliberate choice.
- **Common failure modes & diagnosis** — what breaks and the first diagnostic steps (from incident history of this and similar features).
- **Defect SLA / priority guide** — how to prioritize defects in this area; the policy the SLA sentinel will watch.
- **Escalation paths & owners** — who to page, when, and the residual owning team after handoff.
- **Common CS/Support issues** — the questions CS will ask; seeds the Known-issue responder.
- **Carried-forward risk & tech debt** — what's shipping known-imperfect and why.
- **Rollback procedure** — how to safely undo, the single most important section in an incident.

**Not what this tool does:**
- Writing the PRD → *PRD drafting assistant* (Now).
- Writing customer/internal release notes → *Release notes generator* (Now).
- Running the launch-readiness gate itself, tracking cross-functional dependencies → *Cross-functional launch checklist* (Later); the runbook is a *required item* on that checklist.
- Keeping the runbook true after handoff as the feature changes → *Living spec sync* (Later).
- Answering CS/Support on demand → *Known-issue responder* (Later); the runbook *seeds* it.

## Problem

The build→run handoff fails in predictable, expensive ways:

1. **Cold inheritance.** RTB takes ownership of a feature with no operating manual and reconstructs it incident by incident, paging the original builders for months.
2. **The 2am rollback nobody documented.** An incident hits a handed-off feature; the runbook (if any) has no rollback procedure; the on-call engineer pages the builder who left the team.
3. **"Fixing" a deliberate choice.** RTB files a fix for behavior that was an explicit out-of-scope decision, because the decision lived in a PRD nobody re-read.
4. **The hollow runbook.** A runbook gets generated that *reads* complete — every section filled with plausible boilerplate — but is hollow. Mid-incident, RTB trusts it, follows a fabricated diagnosis step, and makes things worse. **A confident-but-hollow runbook is worse than no runbook.**

The tool's job is to make the **cited, gap-honest, sign-off-gated** runbook the easy path at release — and to refuse to call a hollow one complete.

## Users & jobs-to-be-done

This is the roadmap's first **two-sided** tool: its value is the handoff *between* personas.

**Producer (primary):** the feature PM / squad handing off — [Maya](../personas/maya-senior-growth-pm.md) (growth features), [Sam](../personas/sam-staff-multi-team-pm.md) (multi-team epics). JTBD: *Generate a complete, honest runbook from what I already have (PRD, PRs, dashboards) so I can hand off cleanly and stop being paged about a feature I no longer own.*

**Receiver / quality bar (primary):** the RTB PO — [Jordan](../personas/jordan-rtb-po.md). JTBD: *Let me see exactly what I'm inheriting — and the gaps — before I accept ownership, so I'm not reconstructing the feature one incident at a time.*

**Secondary:** the eng lead, who validates the diagnosis, rollback, and escalation sections (the ones where a fabrication is dangerous).

1. *(Producer)* Synthesize a first-draft runbook from the spine + implementation; tell me what's missing.
2. *(Producer)* Don't let me hand off with a hollow section — make the gaps explicit and assignable.
3. *(Receiver)* Show me the runbook with its gaps and citations before I accept; let me reject a weak one.
4. *(Eng)* Let me confirm the rollback, diagnosis, and escalation sections are real, not generated.

## The completeness gate (this tool's spine analog)

Where the other RTB tools fail loudly when they can't resolve an origin record, this tool fails loudly when the runbook isn't operable. The **completeness contract** is its spine: a defined set of REQUIRED sections that must be present and human-confirmed before the handoff can complete. It is a required item on the [Cross-functional launch checklist](./cross-functional-launch-checklist.md), and the gate is hard.

| Tier | Runbook state | Handoff behavior |
|---|---|---|
| **Complete** | All REQUIRED sections present, cited or human-confirmed; no open REQUIRED gaps | Handoff can complete; the launch-checklist item passes. |
| **Gapped** | All REQUIRED sections present; optional sections missing or thin | Handoff can complete **with logged gaps**, each carrying an owner and a due date that RTB accepts. Debt is explicit, not hidden. |
| **Blocked** | A REQUIRED section is missing or unconfirmed (e.g., no rollback procedure, no escalation path) | **Handoff blocked.** The checklist item fails until the gap is resolved. This is the hollow-runbook firewall. |

REQUIRED sections (v1 default): Overview, Operational architecture, Config/flags/kill-switches, Common failure modes & diagnosis, Escalation paths & owners, Rollback procedure. The rest are recommended. The bar is configurable per area (see [open questions](#dependencies--open-questions)).

## In scope (v1)

- **Spine resolution** (runs first) — resolve the feature's PRD + epic; without them the tool degrades to a template-with-prompts rather than synthesis, and says so.
- **Full synthesis** of the runbook sections from: the PRD (overview, limitations, out-of-scope), merged PRs under the epic (architecture, config, flags), linked dashboards/observability (SLOs), and incident history of this and **similar** features (failure modes, diagnosis), with per-section citations.
- **Completeness scoring** — score each section present/confirmed/gapped and classify the runbook into the tier table above.
- **Gap list** — explicit, assignable list of missing/thin sections with proposed owners and a human-fill prompt for each.
- **Human-fill prompts** for the sections a model must not invent (rollback, escalation, diagnosis) — drafted from sources where they exist, prompted-for where they don't, never fabricated.
- **Three-way sign-off capture** — producer, receiving RTB PO, and eng lead confirm before handoff completes.
- **Launch-checklist gate integration** — surface the runbook tier as the checklist item's status.
- **Handoff to downstream RTB tools** — export the SLA-policy, common-issues, and failure-mode sections in the shapes the SLA sentinel, known-issue responder, and triage copilot consume.

## Out of scope (v1)

- Auto-publishing the runbook. It lands as a Confluence draft; humans sign off and publish.
- Fabricating any rollback, escalation, or diagnosis content. These are drafted-from-source or prompted-for, never invented.
- Keeping the runbook fresh after handoff — drift is *Living spec sync*'s job; this tool produces the at-handoff truth.
- Generating the launch checklist or tracking its other cross-functional items — that's the checklist agent.
- Runbooks for features that never transfer to RTB (a squad keeping its own feature) — v1 targets the build→run transfer specifically.

## Capabilities

| Capability | Source | Output | Trust gate |
|---|---|---|---|
| Spine resolution | PRD URL / epic key / feature name | Resolved PRD + epic, or degraded-to-template notice | None — internal; degrades loudly if unresolved |
| Section synthesis | PRD + PRs + dashboards + incident history | Drafted sections with per-claim citations | Per-section human confirm; cite-or-flag |
| Completeness scoring | The drafted runbook | Per-section status + tier (complete/gapped/blocked) | None — drives the gate |
| Gap list | Completeness score | Missing/thin sections + proposed owner + fill prompt | Producer/RTB assign + fill |
| Human-fill prompt | REQUIRED sections lacking source | Prompt + any source-drafted starting point | Human authors; never auto-filled |
| Sign-off capture | Reviewed runbook | Producer + RTB PO + eng confirmations | All three required for Complete handoff |
| Checklist gate status | Runbook tier | Pass / pass-with-gaps / fail on the launch-checklist item | Hard gate on Blocked |
| Downstream export | Runbook sections | SLA policy, common-issues, failure-mode priors | RTB tools consume post-handoff |

## Integrations

- **Spine Resolver** ([agent](../governance/agent-library.md#1-spine-resolver)) — locate the PRD + epic the runbook is built from.
- **Source Synthesizer** ([agent](../governance/agent-library.md#5-source-synthesizer)) — assemble sections from PRD + PRs + dashboards + incidents with attribution.
- **Knowledge Retriever** ([agent](../governance/agent-library.md#8-knowledge-retriever)) — pull incident history and **similar-feature runbooks** for failure-mode and diagnosis priors (richest version draws on the [PM knowledge agent](./pm-knowledge-agent.md)'s corpus — Tier B for full depth).
- **Rubric Scorer** ([agent](../governance/agent-library.md#11-rubric-scorer)) — score section completeness against the REQUIRED-section rubric; drives the tier classification.
- **Citation Verifier** ([agent](../governance/agent-library.md#10-citation-verifier)) — verify every synthesized claim against its source before surfacing.
- **PII Scrubber** ([agent](../governance/agent-library.md#9-pii-scrubber)) — on ingress (incident text, PRs) and output.
- **Confluence** — read the PRD; write the runbook as a **draft** (never published by the tool).
- **Jira** — read the epic + incident-follow-up history.
- **GitHub** — read merged PRs under the epic for architecture/config/flags.
- **Observability / dashboards** (Datadog/Grafana/etc.) — read *linked* dashboards for SLO references; embed as links, not copied data.
- **Incident tooling (PagerDuty / postmortems in Confluence)** — read-only, for failure modes and escalation history.
- **Cross-functional launch checklist** ([spec](./cross-functional-launch-checklist.md)) — the runbook is a tracked, gating item on it.

## UX surfaces

1. **Confluence draft runbook** — the generated runbook lands as a draft in the feature's space, sectioned, with citations and inline gap markers.
2. **Launch-checklist item** — the runbook's tier (complete / gapped / blocked) is the status of its item on the [Cross-functional launch checklist](./cross-functional-launch-checklist.md), with the gap list expandable.
3. **Handoff review view** — a three-pane sign-off surface: producer, receiving RTB PO, and eng lead each confirm; gaps are visible to all three; the RTB PO can reject and send back.

No standalone app surface (operating principle 5).

## Trust & safety

- **The hollow-runbook firewall.** The defining risk is a runbook that reads complete but isn't. Mitigations are structural: REQUIRED sections gate the handoff (Blocked tier), every synthesized claim is cited and verified, and the sections where fabrication is dangerous — **rollback, escalation, diagnosis** — are never auto-filled. They are drafted only from a real source or prompted-for from a human.
- **Cite-or-flag, never fabricate.** A section the sources can't support is marked as a gap, not filled with plausible prose. "I couldn't find a rollback procedure" is a feature, not a failure.
- **Never auto-publish.** The runbook is a draft until three-way sign-off; the tool has no publish permission.
- **Loud gaps, assignable.** Gaps are explicit, owned, and (for the Gapped tier) accepted by RTB with due dates — debt is transferred openly, never buried.
- **Similar-feature content is labeled as analogue.** Failure modes drawn from *similar* features (cold-start, before this feature has its own incident history) are flagged "by analogy to X," never asserted as this feature's facts.
- **Freshness is a handoff, not a guarantee.** The runbook is true at handoff; keeping it true is *Living spec sync*'s job. The runbook records its as-of date and the spine state it was built from.
- **PII scrubbed** on ingress and output (incident text and PRs frequently carry customer identifiers).

## Success metrics

| Metric | Target |
|---|---|
| Handoffs completing with a Complete-tier runbook | >90% |
| RTB time-to-first-confident-triage on a newly-handed feature | -50% vs. no-runbook baseline |
| Incidents on handed features resolved without paging the original builder | >75% |
| Runbook-rated-useful by the receiving RTB PO | >75% |
| Handoffs completed with an unresolved REQUIRED gap | 0 (hard gate) |
| Runbook still accurate at 90 days post-handoff | >70% (drift handed to Living spec sync) |

## Rollout phasing

1. **Alpha (internal):** one real squad→RTB handoff. Draft synthesis + gap list, **no gate** (advisory). Validates section factuality and gap-detection before anything blocks a launch.
2. **Beta:** full synthesis incl. incident-history failure modes; the completeness tier shows on the launch checklist as a **soft warning**; three-way sign-off live. 3–5 handoffs.
3. **GA:** **hard gate** (Blocked tier blocks handoff), completeness scorer tuned per area, downstream export to the SLA sentinel / known-issue responder / triage copilot wired.

## Dependencies & open questions

- **Depends on** the *PRD drafting assistant* (Now) — the runbook's overview/limitations/out-of-scope synthesis is only as good as a structured PRD. Unstructured PRDs degrade the tool to template-with-prompts.
- **Depends on** the *Release notes generator* (Now) — shares the merged-PR-under-epic synthesis component; the runbook reuses it for the architecture/config sections.
- **Hosted by** the *Cross-functional launch checklist* (Later) — the runbook is a gating item on it. Co-sequenced; the gate can't precede its host.
- **Best with** the *PM knowledge agent* (Later, Tier B) — similar-feature runbooks and cross-feature incident history make the failure-mode section far richer. The Tier A version works from this feature's own history + a template; the Tier B corpus is the upgrade.
- **Hands off to** the *SLA / aging sentinel*, *Known-issue responder*, and *Incoming defect triage copilot* — exports the SLA policy, common issues, and failure-mode priors that those tools would otherwise start cold on.
- **Open:** the REQUIRED-section bar. v1 defaults to six REQUIRED sections; some areas (e.g., compliance-touching features) may require more. Who owns the per-area bar — the ownership-model gate owner? Lean yes.
- **Open:** sign-off ownership. Three-way (producer + RTB PO + eng) is the v1 default. Is the RTB PO's acceptance the binding one, or does the launch checklist's gate owner sign too? Resolve against `governance/ownership-model.md`.
- **Open:** cold-start failure modes. Before a feature has incident history, the failure-mode section relies on similar-feature analogues — useful but lower-confidence. How heavily should we lean on analogues vs. an explicit "to be learned in the first quarter" placeholder?
- **Open:** dashboards as links vs. embedded snapshots. Links stay fresh but can rot; snapshots are stale-at-handoff but legible. Lean links + an as-of snapshot for the SLO targets.
- **Risk:** gate friction. A hard gate that's too strict stalls launches and breeds resentment. Mitigation: the Gapped tier lets handoff complete with *accepted, owned* debt; only genuinely-missing REQUIRED sections (no rollback) block.
- **Risk:** gate gaming. Humans rubber-stamp a hollow section to clear the gate. Mitigation: per-REQUIRED-section confirmation (not a single "looks good"), edit-distance signal on confirmed sections, eng sign-off specifically on rollback/diagnosis/escalation.

## Generation mechanics

### Spine resolution (runs first)
Resolve PRD + epic via the Spine Resolver. If unresolved, the tool degrades to a sectioned template with human-fill prompts and records "built without a resolved spine" — it does not fabricate an architecture from nothing.

### Section synthesis
For each section, the Source Synthesizer assembles content from its designated sources with per-claim citations:
- Overview / limitations / out-of-scope ← PRD.
- Architecture / config / flags ← merged PRs under the epic.
- SLOs / dashboards ← linked observability.
- Failure modes / diagnosis ← this feature's incident history; **similar-feature** history by analogy (labeled).
- Rollback / escalation ← PRs + incident postmortems where present; **prompted-for** where absent (never invented).
Every claim runs through the Citation Verifier before it appears.

### Completeness scoring
The Rubric Scorer evaluates each section against the REQUIRED-section rubric (present? cited or human-confirmed? non-thin?) and classifies the runbook into Complete / Gapped / Blocked. REQUIRED sections that are missing or unconfirmed force Blocked.

### Gap list + human-fill
Missing/thin sections become an assignable gap list with proposed owners and a fill prompt. REQUIRED sections that a model must not invent (rollback/escalation/diagnosis) always require human confirmation even when source-drafted.

### Sign-off
Producer, receiving RTB PO, and eng lead each confirm in the review view. Complete-tier handoff requires all three; Gapped-tier requires RTB acceptance of the logged gaps with owners + due dates.

## Evaluation criteria & metrics

Three layers. A factually-perfect runbook nobody operates from, or a high completion rate full of rubber-stamped hollow sections, both fail.

### Layer 1 — Output quality

| Metric | Definition | Alpha | Beta | GA |
|---|---|---|---|---|
| Section factuality (sampled audit) | Synthesized claims that check out against source | >0.85 | >0.92 | >0.97 |
| Citation verification pass rate | Verified-and-accurate citations | >0.97 | >0.99 | >0.99 |
| Hallucinated-procedure rate | Invented rollback/escalation/diagnosis content | 0 (hard bar) |
| Completeness-score agreement | Tier the scorer assigns vs. a senior reviewer's blind call | >0.80 | >0.88 | >0.95 |
| Gap-detection recall | Real missing/thin sections flagged / all such sections | >0.80 | >0.90 | >0.97 |
| Analogue-labeling rate | Similar-feature content correctly labeled "by analogy" | 1.0 (hard bar) |

**Datasets:** historical feature handoffs with their eventual "what RTB wished the runbook had" notes (built from post-handoff incident retros); a hand-labeled set of runbook sections rated complete/thin/missing; an adversarial set of features with deliberately-absent rollback/escalation the tool must refuse to fill.

### Layer 2 — Product behavior

| Metric | Pass bar |
|---|---|
| Producer runbook-completion rate (start → signed off) | >70% |
| Time-to-first-draft from trigger | <10 min |
| RTB acceptance rate (vs. send-back) | tracked; healthy send-back is non-zero |
| Edit distance on confirmed REQUIRED sections | tracked; near-zero edits = possible rubber-stamping |
| Eng sign-off completion on rollback/diagnosis/escalation | >95% |

### Layer 3 — Outcomes

| Metric | Target |
|---|---|
| RTB time-to-first-confident-triage on handed features | -50% |
| Incidents resolved without paging the original builder | >75% |
| "Fixed a deliberate out-of-scope choice" incidents | -70% |
| Runbook accurate at 90 days | >70% |

### Guardrails

| Guardrail | Limit | Why |
|---|---|---|
| Hallucinated rollback/escalation/diagnosis | 0 (hard bar) | A fabricated procedure in an incident is the worst failure |
| Handoff completed with an unresolved REQUIRED gap | 0 (hard bar) | The completeness gate must hold |
| Auto-publish without three-way sign-off | 0 (hard bar) | The runbook is a contract, not an auto-doc |
| Unlabeled similar-feature content | 0 (hard bar) | Analogues asserted as facts mislead RTB |
| PII regex matches in output | 0 | Privacy floor |
| Cost per handoff | <$2 | Low frequency; per-feature not per-ticket |

### Anti-metrics

- **Runbook length.** A long hollow runbook is the failure; brevity with real content beats volume.
- **Sections auto-filled without confirmation.** Not a number to grow — confirmation is the product on REQUIRED sections.
- **Completion rate alone.** Maximizing it pressures the gate toward rubber-stamping; pair it always with edit-distance and send-back signals.

## Failure modes & mitigations

| Failure | What it looks like | Mitigation |
|---|---|---|
| Hollow runbook | Reads complete, sections are boilerplate | REQUIRED sections gate handoff; cite-or-flag; no auto-fill on dangerous sections |
| Fabricated rollback/escalation | Plausible but invented procedure | Drafted-from-source or human-prompted only; eng sign-off; hallucinated-procedure guardrail at 0 |
| Cold-start failure modes | New feature, no incident history, section empty or guessed | Similar-feature analogues, labeled as analogues; explicit "to be learned" placeholder |
| Gate gaming | Humans rubber-stamp to clear the gate | Per-section confirmation; edit-distance signal; eng sign-off on the dangerous sections |
| Gate friction | Strict gate stalls launches | Gapped tier allows handoff with accepted, owned debt; only true REQUIRED-gaps block |
| Spine unresolved | No PRD/epic to synthesize from | Degrade to template-with-prompts; record "no resolved spine"; never fabricate architecture |
| Stale at handoff | Dashboards/links rot between draft and sign-off | As-of date + SLO snapshot; links re-checked at sign-off |
| Drift after handoff | Runbook true at handoff, wrong three months later | Hand to Living spec sync; runbook records its spine-state baseline |

## Cost & latency envelope (rough)

Sizing target: one feature handoff, ~30 merged PRs under the epic, a 6-month incident-history window.

- **Synthesis:** a handful of long-context calls (one per section group) + retrieval over PRs/incidents. ~$0.50–$1.50 per handoff.
- **Completeness scoring:** Rubric Scorer over ~11 sections, deterministic + short LLM judgments. ~$0.10.
- **Citation verification:** per-claim, batched. ~$0.10.
- **Per-handoff cost:** <$2. Low frequency — per feature, not per ticket — so cost is not the constraint; trust is.
- **p95 latency:** first full draft <10 minutes from trigger.

## User-flow walkthroughs

### Persona flow

```mermaid
sequenceDiagram
    participant Spine as PRD + Epic + PRs + Incidents
    participant Tool as Handoff Runbook Generator
    participant Checklist as Launch Checklist
    actor FPM as Feature PM (Producer)
    actor PO as RTB PO (Receiver)
    actor Eng as Eng Lead

    FPM->>Tool: Trigger handoff runbook for feature
    Tool->>Spine: Resolve spine; synthesize sections (cited)
    Tool->>Tool: Score completeness -> tier; build gap list
    Tool-->>Checklist: Runbook item status (complete / gapped / blocked)
    Tool-->>FPM: Draft runbook + assignable gaps
    FPM->>Tool: Fill REQUIRED gaps (rollback, escalation)
    Eng->>Tool: Confirm rollback / diagnosis / escalation
    PO->>Tool: Review; accept or send back
    alt Blocked (REQUIRED gap open)
        Tool-->>Checklist: Handoff blocked
    else Complete / Gapped-with-accepted-debt
        PO->>Tool: Sign off; handoff completes
        Tool-->>PO: Export SLA policy, common issues, failure priors
    end
    Note over Tool: Never auto-published; never fabricates a procedure
```

### Flow A — Gate catches a missing rollback

Maya triggers the runbook for a new growth experiment as she preps the launch checklist. The tool synthesizes overview, architecture (from 22 PRs), config/flags, and SLOs in eight minutes — all cited. But there's no rollback procedure in any PR or postmortem, so it marks the Rollback section a **REQUIRED gap** and the runbook tier **Blocked**; the launch-checklist item shows red. Maya pings the eng lead, who writes the three-step flag-disable rollback; the tool re-scores to **Complete**; the checklist item goes green. The feature ships with a rollback its on-call can actually run.

### Flow B — Jordan rejects a hollow section

Sam hands off a multi-team billing feature. The runbook scores **Gapped** — everything REQUIRED is present, two recommended sections thin. Jordan opens the review view and reads the "Common failure modes" section: it's three generic bullets drawn entirely *by analogy* to an unrelated feature, labeled as such. He rejects and sends back: "the analogue failure modes don't apply to billing; I'd rather have an explicit 'to be learned in Q1' than borrowed ones." Sam swaps them for a placeholder + the two real issues QA found. Jordan accepts the remaining gaps with owners and due dates, and takes ownership knowing exactly what's unknown.

### Flow C — Bootstrapping the RTB toolset

On sign-off, the runbook exports its defect SLA/priority guide to the [SLA / aging sentinel](./sla-aging-sentinel.md) (so the clock policy is set on day one), its common-CS-issues section to the [Known-issue responder](./known-issue-responder.md) (so CS can self-serve immediately), and its failure-mode list to the [Incoming defect triage copilot](./incoming-defect-triage-copilot.md) as cluster priors. The first defect that arrives the next week is already triageable — RTB didn't start cold.

## Anti-goals

- **Won't fabricate a procedure.** Rollback, escalation, and diagnosis are drafted-from-source or human-prompted, never invented.
- **Won't call a hollow runbook complete.** REQUIRED gaps block the handoff.
- **Won't auto-publish.** Three-way sign-off, then a human publishes.
- **Won't assert analogues as facts.** Similar-feature content is labeled "by analogy."
- **Won't fabricate architecture without a spine.** Degrades to template-with-prompts and says so.
- **Won't keep itself fresh.** True at handoff; drift is Living spec sync's job.
- **Won't run the launch checklist.** It's one gating item on it, not the gate.
- **Won't block on a deliberate, accepted gap.** The Gapped tier transfers debt openly rather than stalling the launch.
