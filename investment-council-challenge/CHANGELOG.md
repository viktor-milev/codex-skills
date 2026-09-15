# Changelog — investment-council-challenge

Release history for the Claude-authored v1 lineage and the Codex/OpenAI port.

---

## v2.0-codex — September 2026

Ported the challenge council from Claude-specific instructions to Codex and OpenAI
models. Added mechanically enforced explicit `$investment-council-challenge` invocation, exact Interactive Brokers
read-tool mappings, provider-response and entitlement handling, gated portfolio reads,
fresh-subagent orchestration for advisors and blind reviewers, dynamic challenger
provenance, host-aware artifact paths, and a strict prohibition on all brokerage
mutation and order-instruction tools. Fixed the chairman's two-versus-three-check
contradiction and moved model-specific history out of the operational entrypoint.
The final audit added no-history subagent spawning, explicit account-access gating,
multi-currency normalization, capacity-aware sizing, a no-capital-decision degraded
state, a no-reliable-pattern option, and repository-safe artifact destinations.

---

## v1.9 — August 2026

First public release of the Claude-authored lineage. Insider flow and the technical setup become standing council
responsibilities rather than incidental observations: both are examined on every
qualifying run, routed through the Position Context Block, and stamped in the Market
Structure Analyst's response, with the top-tier structural-floor condition now
evidence-bound to that row instead of assumed absent. Conviction symmetry extends from
the challenge round to the chairman's own checks, advisor word budgets are differentiated
for the two advisors carrying mandated examinations, and the single-run architecture is
re-confirmed with an explicit tripwire recording what would reopen it. Prepared for
public release with the sibling cross-references to `investment-council` made
version-agnostic.

Versions v1.0–v1.8 were private working versions and were never released publicly. v1.8
forked this variant from `investment-council` v1.7 and added the challenge round —
chairman draft, adversarial audit, chairman re-synthesis, all in one turn — closing the
peer review's collective-miss loop. Everything before v1.8 is the parent skill's history;
see the upstream
[`investment-council`](https://github.com/viktor-milev/claude-skills/tree/main/investment-council)
source for that lineage. The Codex skill has no runtime dependency on that sibling.
