# Engineering's resourcing gap, and the redesign that lets us ship without solving it

- **To:** Product leadership (private — not for engineering circulation in this form)
- **From:** Lance
- **Channel:** 1:1 + follow-up doc link
- **Status:** Draft
- **Purpose:** Surface the resourcing problem behind the current governance, and the redesign that lets us ship without waiting on engineering to solve it. Pairs with the cooperative-framing strategy doc at [`strategy/engineering-optional.md`](../strategy/engineering-optional.md), which is the artifact engineering sees.

---

The governance docs in this repo currently assume engineering is funding a shared platform — CI/CD, k8s, datastores, observability, egress proxy, SSO, secrets vault. Every roadmap item silently inherits that. We have been writing as if those rails exist.

They don't, not at the level the roadmap needs, and engineering has not signalled they will. Product cannot build anything for our own org without engineering today, and engineering has not demonstrated they are committed to adequately resourcing the team that would unblock us. Asking, repeatedly, has not moved the needle. We can keep asking; the roadmap will keep not shipping.

The redesign — drafted as a cooperative strategy doc at [`strategy/engineering-optional.md`](../strategy/engineering-optional.md) — is to stop asking for the platform and ship at a self-sufficient baseline that doesn't need it. We get 11 of 12 roadmap items on substrate we already have: GitHub repos, Claude Code on champion laptops, GitHub Actions for batch agents, and the SaaS tools we already pay for as the state layer. One item — the PM knowledge agent — genuinely needs engineering investment; we mark it Tier B and defer it honestly instead of pretending it's plannable.

## What I want from you

1. **Sign off on shipping at Tier A** without waiting on engineering's commitment. This is a directional call, not a per-tool ask.
2. **Back the framing** when engineering pushes back. They will. The line is: "we are absorbing the failure cost of our own tools by accepting Tier A's narrower scope. We are not asking you to clean up after us." That works if product leadership stands behind it; it doesn't if I'm out there alone.
3. **Hold the line on the data envelope.** Tier A only ships tools that fit inside the existing classification rules — no customer PII, no regulated data, no production database access, only sanctioned SaaS. We aren't bypassing the envelope; we're scoping to what we can own.

## What I am explicitly not asking for

- Authorization to fork from engineering's eventual platform. When they do resource, every Tier B upgrade is a planned migration of a specific tool, not a parallel stack.
- New headcount for product. The champion model from [`strategy/licensed-seats.md`](../strategy/licensed-seats.md) supplies the labour.
- A confrontation with Heather or engineering leadership. The strategy doc is the artifact that goes to them and it's framed as a menu. This memo is for our conversation, not theirs.

## Honest cost

- We will build patterns that don't use engineering's future platform. Recoverable via per-tool migration when the platform lands.
- We will under-monitor at the edges. Mitigated with Slack heartbeats; not a substitute for paging.
- We will look like we're working around engineering. The substantive answer is in the strategy doc; the relational answer is that engineering keeps the option to invest in any Tier B line whenever they decide to.

What we get in return is unambiguous: the roadmap moves from aspirational to plannable, on a timeline product controls. Every quarter we wait for engineering is a quarter of compounding toolkit maturity we do not get back.

Ready to walk through this whenever you have time.
