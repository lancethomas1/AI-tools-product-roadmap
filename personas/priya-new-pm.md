# Priya — New PM

- **Persona type:** User (PM)
- **Tenure:** Junior, 0–2 years as a PM (this is her first PM role)
- **Team:** Single feature squad
- **Lifecycle stages where active:** 1 (Drafting), 2 (Planning)
- **Owner:** Lance
- **Status:** Draft

## Principle

**Priya learns what "good" looks like by reading what the tool produces, not from a blank template.** She is the persona who reads every AI draft end-to-end, edits it carefully, and remembers the changes she made. She is also the persona most at risk if the tool teaches her bad habits. Output quality matters here for a reason beyond Priya's day: she will internalize whatever the tool emits as "this is how PRDs/stories work at this company."

## Snapshot

- 8 months into her first PM role. Came from a strategy/consulting background; no eng experience.
- Owns one feature squad of 4 engineers + 1 designer. One epic at a time.
- Reports to a Senior PM (someone like [Maya](./maya-senior-growth-pm.md)) who reviews her PRDs.
- Has read three "how to write a PRD" Medium articles and the company's PRD template. Each says different things.
- Asks her manager "is this good?" once per artifact. Doesn't always get a clear answer.
- Self-conscious about format. Will spend an hour on the structure of a PRD before writing any content.

## Day in the life

Monday, 10:30am. Priya is staring at a blank Confluence page titled *"PRD: Onboarding email v2."* Her PM brief is half a page in Notion. She knows what the feature should do at a high level. She is not sure: how detailed the success metrics need to be, whether "out of scope" should be a separate section, what the Director will look for when reviewing it, or whether the eng lead will ask about anything she hasn't thought of.

She opens the template, copies the structure, starts the *Problem* section, gets stuck on whether to include a customer quote (the template says "if relevant"), and pivots to writing the *In scope* list because that feels more concrete. She writes five bullets. Two are too vague. She fixes them. She is now 90 minutes in and has not written a single word of *Success metrics* or *Open questions*. Standup is in 12 minutes and she has not opened her ticket board.

After standup she asks her manager "can you take a look?" Her manager will see this version Wednesday afternoon. Priya spends Tuesday writing the rest from a place of low confidence, second-guessing every section.

The pattern is **she is teaching herself the artifact format in real time while the artifact is also the deliverable.** The tools either accelerate her learning loop or they hide the structure from her in a way that leaves her unable to write the next one without them.

## Motivations

| Motivation | What it looks like |
|---|---|
| **Looking competent** | A PRD she submits doesn't come back with "you missed the success metrics section." |
| **Learning the craft** | Each artifact teaches her something about how good PRDs/stories work. |
| **Speed-to-first-draft** | She stops losing whole mornings to the blank page. |
| **Manager's time respected** | Her review asks of her manager are crisp: "is the problem framing right?" not "is this any good?" |

## Frustrations / anti-patterns

| Frustration | What it looks like | How a tool wins (or loses) her |
|---|---|---|
| Blank-page paralysis | She spends 90 minutes on structure before writing content. | **Win:** scaffolded draft with the right sections and the right shape of each. **Lose:** more templates, same blank page. |
| Format-anxiety vs. substance | She gets the format right but the problem framing is weak. | **Win:** the tool's draft is decent on format, leaving her to focus on substance. **Lose:** the tool is sloppy on format and she has to repair both. |
| Tools that hide the structure | A "do it for me" tool produces a polished doc she couldn't have written; she learns nothing. | **Win:** structured output (visible sections, named fields, clarifying-question lists) she can study. **Lose:** opaque prose. |
| Confidence-without-skill | She submits an AI-drafted PRD that's polished but wrong, and discovers at review. | **Win:** the tool says "I'm not sure about this metric — you should set it" instead of fabricating. **Lose:** plausible numbers she didn't supply. |
| Slow review loop with her manager | She has to wait 2 days to learn "you missed X." | **Win:** the tool's DoR/structure check catches what her manager would have caught, before her manager sees it. |

## Jobs-to-be-done

| Stage | Job | Where in the day |
|---|---|---|
| 1. Drafting | "Turn my brief paragraph into a first-draft PRD with all the sections, structured the way my company expects." | Monday morning. |
| 1. Drafting | "Tell me what's missing from this draft before I send it to my manager." | After the first pass, before review. |
| 1. Drafting | "Ask me the questions I should have already thought of." | While drafting. |
| 2. Planning | "Take this PRD section and write the stories that pass our DoR — I don't know all our DoR rules by heart yet." | Refinement prep. |
| 2. Planning | "Generate AC for this story I roughed out, so I can study what good AC looks like." | Mid-week. |

## Roadmap tool fit

| Tool (from ROADMAP) | Role for Priya | Why |
|---|---|---|
| **PRD drafting assistant** (Now) | Primary | The single most valuable tool for her. Compresses her learning loop. |
| **Story & ticket writer** (Now) | Primary | DoR-pass rate matters even more for her than for senior PMs — she doesn't know all the rules. |
| **Weekly status synthesizer** (Now) | Secondary | One squad, low volume; useful but not the bottleneck. |
| **Release notes generator** (Now) | Secondary | Her epics ship less frequently than Maya's. |
| **Backlog grooming copilot** (Next) | Rarely | Her backlog is small and young; less dupe/stale signal. |
| **PM knowledge agent** (Later) | Aspirational | When it exists, she'll use it heavily for "how did past PMs do X?" — a manager-substitute. |

## Tools she lives in today

Confluence, Linear, Slack (her squad's 2 channels + #product-questions), Notion (her personal scratch space), Loom (occasionally for async updates to her manager).

## Success looks like (for Priya)

| Metric | Definition |
|---|---|
| Time from "blank page" to a first-draft PRD she sends for review | Drops from ~half a day to <1 hour. |
| Number of "you missed X" notes from her manager on first review | Drops from 3–5 per PRD to <1. |
| DoR pass rate on her stories at first refinement | Rises from ~40% to >75%. |
| Self-reported confidence in artifact format (quarterly self-eval) | Rises noticeably. |
| Manager review time per PRD | Drops, freeing her manager to give substantive (not format) feedback. |

## Failure modes (how our tools can lose Priya)

| Failure | What she does about it |
|---|---|
| PRD assistant fabricates a success metric, she submits it as-is. | Her manager catches it. Priya is embarrassed; manager loses trust in the tool's output for everyone. |
| Story writer produces polished-but-bad AC, she commits 12 of them. | Eng pushes back at refinement. Priya now over-reviews everything, slower than baseline. |
| Tool's output is so opaque she can't learn from it — she just submits whatever comes out. | After 6 months she is a "PM who uses the tool" but cannot write a PRD without it. **The most dangerous failure mode of all.** |
| DoR check passes a story that shouldn't have passed. | She trusts the check uncritically; eng pushback is sharper. |
| Tool refuses to draft because the brief is thin, with no useful clarifying-question list. | She gives up and writes from scratch with the same blank-page problem. |

## Design implications (special note)

Priya is the persona who reveals a tension built into the [PRD Drafter agent contract](../governance/agent-library.md#2-prd-drafter): the tool should generate enough structure to be useful, but not so much polish that the user stops engaging with the substance. Two specific implications:

1. **Show the scaffold, not just the prose.** Sections, named fields, and explicit "this section is empty — you should write it" placeholders are more valuable to Priya than well-written paragraphs that hide the structure.
2. **Surface clarifying questions prominently.** A brief-too-thin response should not be a refusal; it should be a clarifying-question list Priya can use as a checklist for her own thinking.

Both of these are already in the agent contract. Priya is the persona that anchors why.

## Quotes (illustrative, not real)

> "I learn how to write a PRD by reading what comes back, not by being given a blank template."

> "Don't make something up — tell me what I should be thinking about and let me fill in the answer."

> "If I can't write the next PRD on my own after using the tool ten times, the tool is making me worse at my job."

## Version history

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-27 | Lance | Initial draft of Priya — New PM archetype. |
