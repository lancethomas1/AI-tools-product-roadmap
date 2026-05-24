# Product-owned tooling: who gates the code

- **To:** Heather
- **Channel:** Slack / 1:1
- **Status:** Draft
- **Purpose:** Float a governance POV — engineering owns guardrails, product owns the gate on product-owned internal tools — before raising it more formally.

---

Hey Heather — wanted to float a point of view I've been forming and get your read before I take it further.

The old dividing line — engineering ships code, product doesn't — was never really about code. It was about accountability. Engineering gatekept because engineering owned the consequences when something broke in front of a customer. AI hasn't erased that logic; it's just decoupled it from the org chart. Producing working code is no longer the scarce skill that justifies engineering being the sole gate.

So the right question isn't "who can push code" — it's "who owns the end user." Where engineering owns end-user support — guest-facing apps, revenue-supporting employee tools — engineering should stay the gatekeeper, because they carry the blast radius. But product is increasingly building for its own org and workflows. Those tools have repos too, and for them product owns the users, the support, and the consequences. Engineering as the default gate there adds friction without adding accountability.

Two clarifications so this lands as a governance proposal and not a turf claim:

First, "gatekeeper" is doing too much work, so let me split it. Engineering owning platform standards, security guardrails, and deployment surface is not the same as engineering approving every change. I'm fully conceding the first. My claim narrows to: engineering shouldn't be the approval bottleneck on product-owned internal tools.

Second, on the obvious security/data question — prod access, shared infra, sensitive data — these tools still live inside engineering's guardrails. Within those rails, product self-serves. The rails stay; the per-change approval gate goes.

To be concrete, this isn't hypothetical — it's what we're already building for ourselves: AI assistants for PRD drafting, story and ticket writing, release notes generation, weekly status synthesis, backlog refinement, stakeholder comms tailoring, spec-to-sprint decomposition, turning meetings into artifacts, PM knowledge retrieval, keeping specs in sync, cross-functional launch checklists, and proactive sprint management. Every one spans our own delivery lifecycle, and every one will live in a repo. None has an end user outside our own org — which is exactly the point: product owns the users, the support, and the consequences, so product should own the gate.

Curious where you push back, and whether this is worth raising more formally.
