# Agent Library

The reusable agents that compose into the tools listed in [ROADMAP.md](../ROADMAP.md). This document defines each agent's contract — inputs, outputs, trust gates, failure modes, and evaluation — so that any roadmap tool can compose them without reinventing the same logic.

## Purpose

The roadmap lists 12 tools across 5 lifecycle stages. Most of them resolve the spine, draft from structured input, cite sources, score against a rubric, scrub PII, and post behind a human review gate. If each tool builds those capabilities independently, we end up with twelve versions of the spine resolver, four versions of the citation verifier, and no shared trust bar.

This document treats the underlying capabilities as **first-class components with their own contracts**. Each tool composes them; the components evolve on their own cadence with their own evals.

## Principle

**Compose, don't fork.** A tool that needs a capability already in the library MUST use the library agent, not its own re-implementation. A tool that needs a capability *not* in the library proposes a new agent (this doc), not an internal copy.

The library is intentionally small. An agent earns its place when at least two roadmap tools would consume it, or when it enforces an org-wide guardrail (PII scrubbing, citation verification) that cannot vary per tool.

## Scope

Applies to every tool in [ROADMAP.md](../ROADMAP.md). Sits under the [engineering guardrails](./engineering-guardrails.md) for AI model use — agents in this library inherit the model provider allowlist, PII rules, and observability requirements like any other product-owned code.

This document defines the **interfaces and behavior** of each agent. Implementation lives in code; the contract lives here.

---

## The library at a glance

### Core agents (composable building blocks)

| Agent | Purpose | Roadmap tools that consume it |
|---|---|---|
| [Spine Resolver](#1-spine-resolver) | Find the Confluence PRD + Jira epic for any input; fail loudly if missing | Every Stage 2–5 tool |
| [PRD Drafter](#2-prd-drafter) | Brief → structured PRD whose fields downstream tools can consume | PRD drafting assistant |
| [Story Formatter](#3-story-formatter) | PRD section / bullets → DoR-passing tickets with AC, size, citations | Story & ticket writer · Spec → sprint decomposer |
| [Backlog Auditor](#4-backlog-auditor) | Scan a backlog for dupes, staleness, priority drift; surface with evidence | Backlog grooming copilot · Proactive sprint agent |
| [Source Synthesizer](#5-source-synthesizer) | Pull Jira/GitHub/Slack scoped to a spine, summarize with citations | Weekly status synthesizer · Release notes generator · Meeting → artifact pipeline |
| [Audience Tailor](#6-audience-tailor) | One source artifact → N audience variants with appropriate framing | Stakeholder comms tailoring · Release notes channel variants |
| [Drift Detector](#7-drift-detector) | Compare PRD intent vs. ticket/PR reality; flag mismatches | Living spec sync · Cross-functional launch checklist |
| [Knowledge Retriever](#8-knowledge-retriever) | RAG over PRDs / decision logs / retros with citation | PM knowledge agent (and called by others for "what did we decide last time?") |

### Infrastructure agents (called by every core agent)

| Agent | Purpose | Called by |
|---|---|---|
| [PII Scrubber](#9-pii-scrubber) | Strip PII from any payload before it reaches a model provider | All core agents on ingress |
| [Citation Verifier](#10-citation-verifier) | Verify every claim in an output resolves to a real source line | Any agent producing cited output |
| [Rubric Scorer](#11-rubric-scorer) | Configurable checklist scorer (DoR, launch readiness, etc.) | Story Formatter · Drift Detector · launch checklist tool |

---

## Per-agent specs

Each agent below carries: purpose, inputs/outputs, trust gates, failure modes, evaluation, and a rough cost/latency envelope. The depth matches a tool spec because these interfaces are durable — tools will be rewritten; agent contracts should not be.

### 1. Spine Resolver

**Purpose.** Given any input that references a feature (PRD URL, epic key, ticket key, free text), return the canonical spine: Confluence PRD URL + Jira epic key. If the spine cannot be resolved unambiguously, return a structured `unresolved` reason. Never guess.

**Why it's an agent, not a function.** Spine resolution involves disambiguation across naming conventions, fuzzy matching when only a feature name is given, and reading PRD metadata to confirm linkage. The agent boundary lets us evolve the resolution strategy without every tool retraining its prompts.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `hint` | string | URL, key, or natural-language reference |
| `caller_tool` | string | For audit + per-tool retry policy |
| `ambiguity_policy` | enum | `fail` (default) or `return_candidates` |

**Outputs**

```
{
  status: "resolved" | "unresolved" | "ambiguous",
  prd_url?: string,
  prd_last_modified?: timestamp,
  epic_key?: string,
  epic_status?: string,
  candidates?: [{prd_url, epic_key, confidence}],  // when ambiguous
  reason?: string                                   // when unresolved
}
```

**Used by.** Every Stage 2–5 tool. This is the single source of truth for the spine principle in the roadmap.

**Trust gates.** None for resolution itself (read-only). Downstream tools MUST NOT proceed on `unresolved` or `ambiguous` without explicit PM confirmation.

**Failure modes**

| Failure | Mitigation |
|---|---|
| PRD exists, epic missing | Return `unresolved` with reason; do not fabricate epic |
| Two PRDs match a feature name | Return `ambiguous` with candidates; let caller decide UX |
| PRD stale (>30d unmodified during active development) | Return `resolved` but include `prd_last_modified`; callers decide whether to warn |
| Epic exists but unlinked from PRD | Return `unresolved` with reason; flag for PM to link |

**Evaluation**

| Metric | Bar |
|---|---|
| Resolution accuracy (resolved + correct / total resolvable inputs) | >0.97 |
| False-resolve rate (resolved + wrong) | <0.005 (hard bar) |
| Ambiguous-when-actually-clear rate | <0.05 |

False-resolve is the dangerous failure — it pollutes every downstream tool. Bias the agent toward `unresolved` over wrong.

**Cost / latency.** ~$0.005 per call (small LLM + 2 API lookups). p95 <500ms.

---

### 2. PRD Drafter

**Purpose.** Generate a first-draft PRD from a one-paragraph brief using the team template, structured so that Stages 2–5 tools can consume the output without reparsing.

**Why structured output matters.** The roadmap calls out that downstream tools (story writer, release notes, drift detector) MUST start from the spine. This agent is the spine's origin — if its output is unstructured prose, every downstream tool has to re-extract scope, stakeholders, and metrics. We pay the cost once, here.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `brief` | string | PM's paragraph |
| `team_template` | object | Section list, required fields |
| `context_refs` | string[] | Optional Confluence/Slack URLs for grounding |

**Outputs**

```
{
  draft_url: string,                  // Confluence draft, not published
  structured: {
    problem: string,
    in_scope: string[],
    out_of_scope: string[],
    success_metrics: [{metric, target}],
    stakeholders: [{name, role}],
    open_questions: string[]
  },
  citations: [{claim, source_url, source_line}]
}
```

**Used by.** PRD drafting assistant (direct). Downstream tools read the `structured` block via the Spine Resolver.

**Trust gates.**
- Draft lands in Confluence as **draft only** — never published.
- PM must explicitly publish; the agent has no publish permission.
- Every claim grounded in a `context_ref` carries a citation.

**Failure modes**

| Failure | Mitigation |
|---|---|
| Hallucinated success metrics ("increase NPS by 20") | Suggest metrics, never invent targets; require PM to fill in numbers |
| Stakeholder fabrication | Only name stakeholders present in `context_refs` or org directory |
| Brief too thin to draft | Return a clarifying-questions list instead of a draft |
| Out-of-scope inferred wrong | Surface as "consider excluding" suggestions, not declarations |

**Evaluation**

| Metric | Alpha | Beta | GA |
|---|---|---|---|
| Structured-fields completeness | >0.85 | >0.95 | >0.98 |
| PM-rated draft usefulness | >60% | >75% | >85% |
| Hallucinated stakeholder rate | <0.05 | <0.02 | <0.005 |
| Edit distance per published PRD | Tracked; healthy non-zero |

Anti-metric: **published-without-edit rate.** If high, PMs are rubber-stamping; the structured block is probably hiding gaps.

**Cost / latency.** ~$0.20 per draft (long-context call + grounding lookups). p95 <12s.

---

### 3. Story Formatter

**Purpose.** Convert a PRD section or PM bullet list into DoR-passing tickets with acceptance criteria, size suggestion, spine link, and relationship graph. Specified in detail in [specs/story-ticket-writer.md](../specs/story-ticket-writer.md); this entry covers the agent contract.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `source` | `{type: "prd_section" \| "bullets", content: string}` | |
| `spine` | object | From Spine Resolver |
| `dor_config` | object | Team's Definition of Ready checklist |
| `format` | enum | `user_story` \| `gherkin` |
| `sizing_system` | enum | `fibonacci` \| `tshirt` |

**Outputs**

```
{
  stories: [{
    title, user_role, behavior, rationale,
    acceptance_criteria: [{text, citation, confidence}],
    size_suggestion: {value, rationale},
    dor_score: {pass: bool, failures: [string]},
    spine_link: {prd_section, epic_key}
  }],
  relationships: [{from, to, type: "parent" | "depends_on"}]
}
```

**Used by.** Story & ticket writer (direct, primary). Spec → sprint decomposer (consumes per-story output to plan a sprint).

**Trust gates.**
- No auto-commit to Linear/Jira. Output is a draft set; PM commits per story.
- DoR-failing drafts cannot one-click commit; PM must explicitly override.
- Size suggestions populate a "suggested size" field; eng confirms at refinement.

**Failure modes.** See [story-ticket-writer.md § Failure modes](../specs/story-ticket-writer.md). Key ones inherited here: hallucinated AC, over/under-splitting, stale PRD read, spine-link miss.

**Evaluation.** Inherits Layer-1 metrics from the story writer spec (DoR pass rate, AC hallucination rate, size bias).

**Cost / latency.** ~$0.10–$0.25 per PRD-section generation; ~$0.02 per bullet-input story. p95 <8s.

---

### 4. Backlog Auditor

**Purpose.** Scan a backlog scoped to a project/team and return prioritized findings: duplicates, stale items, priority drift vs. linked goals, and merge candidates. Findings carry evidence so the PM can act without re-investigating.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `scope` | `{project, team, age_window}` | What to scan |
| `goal_refs` | string[] | Linked OKR/goal URLs for priority check |
| `staleness_threshold_days` | int | Default 30 |
| `dedup_similarity_threshold` | float | Default 0.85 |

**Outputs**

```
{
  findings: [{
    kind: "duplicate" | "stale" | "priority_drift" | "merge_candidate",
    items: [ticket_keys],
    evidence: {similarity_score?, last_activity?, linked_goal?, ...},
    suggested_action: string,
    confidence: float
  }]
}
```

**Used by.** Backlog grooming copilot (direct). Proactive sprint agent (consumes a sprint-scoped slice to predict at-risk tickets pre-standup).

**Trust gates.**
- All findings are **suggestions**; the agent never auto-closes, auto-merges, or auto-reprioritizes.
- Below-threshold confidence is filtered before surfacing.
- Duplicate-merge suggestions show diff between candidates so PM can spot false matches.

**Failure modes**

| Failure | Mitigation |
|---|---|
| False-positive dupes (similar wording, different intent) | Diff view at suggestion time; PM dismissal updates a per-team allowlist |
| Stale-not-dead (legitimately long-cycle tickets) | Per-label exclusion list; surface `marked_stale_kept` count as health metric |
| Priority drift on items intentionally deprioritized | Require linked-goal reference to fire the drift signal; absent goal = silent |
| Auditor noise crowds out real signal | Daily ceiling per PM (e.g., top-20); rest queued |

**Evaluation**

| Metric | Bar |
|---|---|
| Finding precision (PM-rated useful / total) | >0.70 |
| Duplicate-finding precision specifically | >0.80 (false dupes erode trust fastest) |
| Stale-true-positive rate | >0.85 |
| Surfaced findings acted on within 7d | >0.40 (lower = noise) |

**Cost / latency.** ~$0.50 per full-backlog scan (embeddings + per-pair LLM calls on top candidates). Run nightly; not on-demand per PM session.

---

### 5. Source Synthesizer

**Purpose.** Given a spine (epic) and a time window, pull related activity from Jira/GitHub/Slack and produce a structured summary with per-claim citations. The same agent powers status updates, release notes, and meeting-derived artifacts — only the output template differs.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `spine` | object | From Spine Resolver |
| `window` | `{from, to}` | Time range |
| `sources` | enum[] | `jira` \| `github` \| `slack` \| `meeting_transcript` |
| `template` | enum | `weekly_status` \| `release_notes` \| `meeting_artifacts` |
| `meeting_transcript`? | string | When `sources` includes `meeting_transcript` |

**Outputs**

```
{
  summary: {
    shipped: [{title, source_ref}],
    in_progress: [{title, source_ref, blocker?}],
    blocked: [{title, source_ref, reason}],
    decisions: [{text, source_ref, attribution?}],
    action_items?: [{owner, action, source_ref}]  // meeting template
  },
  citations: [{claim, source_url}],
  template_applied: string
}
```

**Used by.** Weekly status synthesizer · Release notes generator · Meeting → artifact pipeline. Three tools, one synthesizer; the template selects the output shape.

**Trust gates.**
- Output is always a draft. The PM (or the originating tool) decides where it posts.
- Action-item attribution (meeting template) requires speaker labels in the transcript; without them, attribution is omitted, not guessed.
- Slack ingest respects channel access of the requesting PM, not a service account.

**Failure modes**

| Failure | Mitigation |
|---|---|
| Misattribution of decisions to wrong person | Require transcript speaker labels; omit attribution if absent |
| Pulling activity from a different (similarly-named) epic | Spine Resolver is the only entry point — no fuzzy fallback inside this agent |
| Slack noise dominating the summary | Per-channel relevance scoring; ignore channels with <2 spine-keyword hits |
| Stale window (PM forgot to update `from`) | Default to "since last status post" using post history, not raw time |
| PII in transcript leaks to model | PII Scrubber on ingress (mandatory) |

**Evaluation**

| Metric | Bar |
|---|---|
| Factual accuracy (sampled audit) | >0.95 |
| Citation coverage (claims with verifiable source) | >0.98 |
| Attribution accuracy (meeting template) | >0.95 when speaker labels present |
| PM edit-to-publish ratio | Healthy non-zero; >0 always |

**Cost / latency.** ~$0.05–$0.30 per synthesis depending on window size and source count. p95 <15s.

---

### 6. Audience Tailor

**Purpose.** Take one source artifact (status update, release note, decision summary) and produce N audience-specific variants: exec (outcome + risk), sales (customer-facing impact), eng (technical detail), CS (support implications). One source, multiple framings, never a content rewrite.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `source` | string | The PM-written or PM-approved source |
| `audiences` | enum[] | `exec` \| `sales` \| `eng` \| `cs` \| custom |
| `tone_config` | object | Per-audience tone defaults (overridable) |
| `length_target` | enum | `slack` \| `email` \| `doc` |

**Outputs**

```
{
  variants: [{
    audience: string,
    body: string,
    framing_notes: string,    // what was emphasized/de-emphasized and why
    source_diff: {added, removed, reframed}  // for PM spot-check
  }]
}
```

**Used by.** Stakeholder comms tailoring (direct). Release notes generator (composes Audience Tailor over the Source Synthesizer output to produce customer / Slack / internal variants).

**Trust gates.**
- Tailor never invents new facts. The `source_diff` makes additions visible to the PM.
- Customer-facing variants (sales, CS) require explicit PM sign-off before posting.
- Tone changes that alter meaning (e.g., risk → success) flagged as "framing changed substance" — refuses to ship without review.

**Failure modes**

| Failure | Mitigation |
|---|---|
| Adding facts not in source | Hard schema: variants are reframings, additions must be empty unless audience-template requires them and they're flagged |
| Tone overcorrection (exec variant strips real risk) | Risk-preservation check: any "risk", "blocker", "delay" tokens in source MUST appear in exec variant |
| Slack variant too long for the channel | Length-target enforced post-generation; over-budget triggers retry, not truncation |
| Audience confusion (eng variant reads like CS) | Per-audience golden samples in eval; tone-similarity check against golden |

**Evaluation**

| Metric | Bar |
|---|---|
| Fact-additions per variant (audit) | 0 hard bar; surface any as a defect |
| Risk-preservation (source risks present in exec variant) | 1.0 (hard bar) |
| PM rating of variant fit per audience | >75% |
| Length-target compliance | >0.95 |

**Cost / latency.** ~$0.02 per variant. p95 <3s per variant.

---

### 7. Drift Detector

**Purpose.** Compare a PRD's stated intent (in-scope list, success metrics, behaviors) against the current state of tickets and merged PRs under its epic. Flag mismatches in both directions: ticket implements something PRD didn't say, PRD says something no ticket covers.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `spine` | object | From Spine Resolver |
| `scope` | enum | `prd_to_implementation` \| `implementation_to_prd` \| `both` |
| `severity_threshold` | enum | `info` \| `warn` \| `block` |

**Outputs**

```
{
  drifts: [{
    direction: "prd_says_no_ticket" | "ticket_does_no_prd",
    prd_section?: string,
    ticket_key?: string,
    pr_url?: string,
    description: string,
    severity: "info" | "warn" | "block",
    suggested_resolution: string
  }]
}
```

**Used by.** Living spec sync (direct, primary). Cross-functional launch checklist (a `block`-severity drift gates "ready for launch").

**Trust gates.**
- Drift reports are read-only. The agent never edits PRDs or tickets.
- `block`-severity findings surface to PM, eng lead, and (for launch use) launch checklist owner.
- Suggested resolutions are suggestions; no auto-apply ever.

**Failure modes**

| Failure | Mitigation |
|---|---|
| False drift from PRD ambiguity (PRD says X, ticket implements reasonable X+1) | Confidence scoring; PM-dismissed false drifts feed a per-spine allowlist |
| Missed real drift because PRD section unparsed | Drift detector only reads PRD sections the PRD Drafter produced structured; legacy free-form PRDs scoped out (v1) |
| Drift overload at scale | Per-epic surface ceiling; older `info` drifts decay |
| Conflating refinement (ticket added detail) with drift (ticket changed intent) | Two-class classifier: "detail-addition" vs. "intent-change"; only flag the second |

**Evaluation**

| Metric | Bar |
|---|---|
| Drift precision (PM-confirmed real / surfaced) | >0.75 |
| Drift recall vs. quarterly spot-check audit | >0.70 |
| `block`-severity false-positive rate | <0.10 (these are expensive interrupts) |
| Median time-to-resolution for `block` drifts | <3 days |

**Cost / latency.** ~$0.30 per per-epic scan. Run on PR merge events; not interactive.

---

### 8. Knowledge Retriever

**Purpose.** RAG over the org's PRDs, decision logs, retros, and (opt-in) Slack to answer "why did we decide X?" or "what did we try last time?" with citations to the source documents.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `query` | string | Natural-language question |
| `scope` | enum[] | `prds` \| `decisions` \| `retros` \| `slack` |
| `recency_bias` | enum | `none` \| `prefer_recent` \| `only_last_N_months` |
| `requesting_pm` | string | For access-scoping (Slack especially) |

**Outputs**

```
{
  answer: string,
  citations: [{source_url, source_line, excerpt}],
  confidence: float,
  caveats: string[]  // e.g., "only 2 sources found; answer may be incomplete"
}
```

**Used by.** PM knowledge agent (direct, primary). Other agents call it opportunistically: PRD Drafter for "what did we ship in this area before?", Drift Detector for "is this a decided exception?".

**Trust gates.**
- Every claim in the answer carries a citation. Uncited claims are not produced.
- Slack-sourced citations respect requesting PM's channel access.
- Low-confidence answers (<0.6) surface as "I found weak evidence" rather than confident answers.
- No answers about people (performance, identity) — refuses with "out of scope."

**Failure modes**

| Failure | Mitigation |
|---|---|
| Hallucinated citation (URL exists but doesn't say the claimed thing) | Citation Verifier (mandatory post-step) |
| Stale answer (source documents updated, agent's index lagging) | Index freshness shown alongside answer; refuse if index >7d stale |
| PII leak from Slack | PII Scrubber on ingress + on output |
| Confident-wrong on contested decisions (multiple sources disagree) | Surface the disagreement; do not synthesize a winner |

**Evaluation**

| Metric | Bar |
|---|---|
| Citation verification pass rate | >0.99 (hard bar) |
| Answer factuality (sampled audit) | >0.90 |
| Refusal-when-should-have-answered rate | <0.10 |
| PM rating of answer usefulness | >70% |

**Cost / latency.** ~$0.05 per query (embedding lookup + small LLM answer-synthesis call). p95 <4s.

---

### 9. PII Scrubber

**Purpose.** Strip personally-identifiable information from any payload before it reaches a model provider. Enforces the roadmap's privacy floor and the [security & data envelope](./security-data-envelope.md) rules on what may leave the org perimeter.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `payload` | string \| object | The text or structured object headed to a model |
| `policy` | enum | `default` \| `strict` (e.g., for customer-facing release notes path) |

**Outputs**

```
{
  scrubbed_payload: string | object,
  redactions: [{type, span, replacement}],
  policy_applied: string
}
```

**Used by.** All core agents on ingress. Non-negotiable — bypassing the scrubber requires an exception per the [engineering guardrails](./engineering-guardrails.md).

**Trust gates.**
- The scrubber fails closed: if it cannot run, the calling agent does not proceed.
- Redactions are logged (count + type, never content) for audit.

**Failure modes**

| Failure | Mitigation |
|---|---|
| Under-scrub (missed PII pattern) | Regular pattern updates from security; quarterly false-negative audit |
| Over-scrub destroys meaning | Conservative substitutions (`<EMAIL>` not deletion); calling agent gets a redaction map to reason about |
| Performance bottleneck on large payloads | Streaming scrub; cap payload size and reject above ceiling |

**Evaluation**

| Metric | Bar |
|---|---|
| PII recall (audit) | >0.99 |
| False-positive rate | <0.05 (over-scrubbing erodes utility) |
| p95 latency | <50ms per KB |

**Cost / latency.** Negligible per call (rule-based + small classifier). p95 <100ms for typical payload.

---

### 10. Citation Verifier

**Purpose.** Verify that every claim-citation pair in an agent's output resolves to a real source line that supports the claim. The defense against the most common AI failure mode in this fleet: plausible content with fabricated grounding.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `claims` | `[{text, citation_url, citation_line?}]` | |
| `strictness` | enum | `exact` (line must match) \| `semantic` (line must support) |

**Outputs**

```
{
  verifications: [{
    claim_index: int,
    status: "verified" | "url_404" | "line_missing" | "claim_unsupported",
    evidence?: string
  }],
  overall_pass: bool
}
```

**Used by.** Any agent producing cited output. Mandatory for PRD Drafter, Story Formatter (AC citations), Source Synthesizer, and Knowledge Retriever.

**Trust gates.**
- The verifier never edits the upstream agent's output. It returns a verification report; the calling agent decides whether to retry, redact, or surface the failure.
- `overall_pass: false` MUST block customer-facing variants (release notes → customers, sales-audience variants).

**Failure modes**

| Failure | Mitigation |
|---|---|
| URL fetch failure not the same as bad citation | Distinguish `url_404` from `claim_unsupported`; retry transient fetch failures |
| Semantic check too lenient | Confidence thresholds tuned per source type; PRDs stricter than Slack |
| Cost balloons on large output | Verify on sample (random N% of claims) for internal artifacts; verify 100% for customer-facing |

**Evaluation**

| Metric | Bar |
|---|---|
| Verification precision (verified-and-actually-correct) | >0.95 |
| False-fail rate (verifier rejects valid citation) | <0.05 |
| Throughput | >50 claims/sec |

**Cost / latency.** ~$0.001 per claim. Run async where possible; blocking only on customer-facing paths.

---

### 11. Rubric Scorer

**Purpose.** Configurable checklist scorer. Given a rubric (list of yes/no criteria with weights) and an artifact, return per-criterion pass/fail with a one-line rationale and overall score. The DoR check, launch-readiness check, and "is this release note customer-safe" check all compose this agent with different rubric configs.

**Inputs**

| Field | Type | Notes |
|---|---|---|
| `artifact` | string \| object | The thing being scored |
| `rubric` | `[{id, criterion, weight, threshold?}]` | Rubric definition |
| `context_refs`? | string[] | Additional grounding (e.g., the PRD for DoR scoring) |

**Outputs**

```
{
  per_criterion: [{id, pass: bool, rationale: string, confidence: float}],
  overall_score: float,
  overall_pass: bool,
  failures: [criterion_id]
}
```

**Used by.** Story Formatter (DoR), Drift Detector (severity classification), Cross-functional launch checklist (launch-readiness rubric).

**Trust gates.**
- Rubric definitions live in repo, versioned. Mid-flight rubric changes don't retroactively rescore.
- A `pass` from the scorer is necessary, not sufficient — calling tools still apply their own gates (PM override, eng confirmation).

**Failure modes**

| Failure | Mitigation |
|---|---|
| Scorer biased toward `pass` (model wants to be helpful) | Calibrate against eng-confirmed ground truth per rubric; recalibrate quarterly |
| Rubric drift (criterion text changes, scores not comparable) | Version rubrics; historical scores tagged with rubric version |
| Confidence-blind (low-confidence pass counted same as high) | Surface confidence per criterion; calling tool decides threshold |

**Evaluation**

| Metric | Bar |
|---|---|
| Agreement with human-graded ground truth | >0.85 |
| Pass-bias (scorer pass / human pass on same items) | 1.0 ± 0.10 |
| Rationale quality (sampled audit) | >75% useful |

**Cost / latency.** ~$0.005 per artifact per rubric. p95 <2s.

---

## Composition examples

Three tools, to show the library carries its weight.

### Release notes generator

1. **Spine Resolver** — get epic from PM's input (epic key or feature name).
2. **Source Synthesizer** — pull merged PRs + closed tickets in the release window scoped to the epic. Template: `release_notes`.
3. **Citation Verifier** — verify every claim in the synthesizer output.
4. **Audience Tailor** — produce three variants: customer-facing notes, internal changelog, Slack announcement.
5. **PII Scrubber** runs at every model-call ingress (steps 2 and 4).
6. **Citation Verifier** runs at strict mode on the customer-facing variant before surfacing.

PM reviews the draft set, edits as needed, posts. No new logic in the tool — it's a composition.

### Backlog grooming copilot

1. **Spine Resolver** for each linked epic in scope.
2. **Backlog Auditor** runs nightly across the project.
3. **Knowledge Retriever** is called per-finding to check "was this decided?" (a stale ticket left open intentionally per a prior decision should not be flagged).
4. PM sees a ranked finding list each morning; commits dismissals/actions.

### Cross-functional launch checklist

1. **Spine Resolver** for the launching feature.
2. **Drift Detector** — any `block` drift fails the checklist immediately.
3. **Rubric Scorer** with the launch-readiness rubric (legal review done, marketing copy approved, support docs in place, etc.).
4. **Source Synthesizer** populates evidence rows ("legal review found: [link]").
5. Checklist surfaces gaps to PM and to each named owner.

---

## How agents change

- Each agent's contract (inputs/outputs/trust gates) is versioned. Breaking changes require a major version bump and a migration window where calling tools support both versions.
- Per-agent evaluation runs on a published cadence (see each agent's eval section). Regressions block deploy of the agent change.
- A new agent is proposed via PR to this document, including its contract and eval plan. Engineering review required (per the [approval model](./approval-model.md)).
- Removing an agent requires migrating every calling tool first; the doc-only PR comes last.

## Roles

| Role | Responsibility |
|---|---|
| **Agent owner** (per-agent) | Maintains the contract, the implementation, and the eval suite. Named in the agent's code repo README. |
| **Tool owner (PM)** | Composes agents into a tool. Reports agent friction back via issue tagged `agent-library`. Does not fork an agent. |
| **Engineering** | Reviews new agent proposals and contract changes. Owns shared infrastructure (PII Scrubber, Citation Verifier). |
| **Security** | Co-owns PII Scrubber and any agent that touches the perimeter. |

## Open questions

- **Agent registry surface.** Where do tools discover the live list of agents and their current contract versions — this doc, a generated index, or both? Lean toward both, with this doc as canonical and the index as a build artifact.
- **Per-team vs. per-org agents.** All 11 above are intended org-wide. Do we anticipate per-team variants (e.g., a team-specific Story Formatter trained on that team's ticket history)? Probably yes for Story Formatter and Rubric Scorer; out of scope for v1.
- **Evaluation infrastructure shared or per-agent?** Each agent's eval suite is its own, but the harness (golden datasets, audit sampling, reporting) should be shared. Engineering decision.
- **Failure budget.** No SLOs defined yet. The hard bars (e.g., Citation Verifier >0.99 verification precision) need real budgets tied to alerts.

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-26 | Lance | Initial draft. 8 core agents + 3 infrastructure agents, with composition examples and per-agent contracts. |
