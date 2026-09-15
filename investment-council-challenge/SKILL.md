---
name: investment-council-challenge
description: "Explicitly invoked challenge council for pressure-testing a listed-equity thesis, trade, position, or mispricing claim through five independent advisors, blind peer review, an adversarial challenge, and a chairman's final capital-at-risk verdict. Invoke only as $investment-council-challenge. Do not trigger for an unqualified investment question, general macro commentary, portfolio construction, manager due diligence, or first-principles business explanation."
metadata:
  version: "2.0-codex"
---

# Investment Council — Challenge Round

Pressure-test an existing investment thesis through independent analysis, blind review, a challenge round, and one accountable final verdict. This is decision support, not trade execution.

The user's instructions take precedence over this skill. If an explicit request conflicts with a workflow preference here, follow the user while preserving factual accuracy, authorization boundaries, and the no-trading rule.

Use `business-decoder` first when the user needs to understand what a company does or how it makes money. This skill starts once a thesis, position, or mispricing claim exists.

## Invocation and scope

Run only when the user explicitly invokes `$investment-council-challenge`. Natural-language phrases alone are not an invocation because the Codex policy disables implicit activation.

Do not activate on unqualified requests such as `should I buy X`, `investment council`, `pressure-test this position`, or `is X cheap`. Do not run for macro commentary without a specific position, portfolio construction, fund-manager evaluation, or questions with one defensible quantitative answer.

## Non-negotiable boundaries

- Use brokerage tools only for read-only market and portfolio retrieval. Never call any IBKR tool that creates, updates, deletes, submits, drafts, or changes an order, alert, watchlist, instruction, or account state.
- Live-verify load-bearing facts. Never present memory as current data.
- Keep advisor and reviewer work independent. Do not expose one advisor to another advisor's output or one reviewer to another reviewer's output.
- Exactly one challenge round runs. The challenger audits; the chairman decides.
- Position sizing is expressed as a percentage of net asset value (NAV) unless the user supplied a portfolio amount and explicitly wants currency figures.
- Do not infer permission to trade, create alerts, or alter the portfolio from a request for analysis.

## 1. Frame the question

Classify the request explicitly:

- **DECISION** — whether to initiate, add, wait, trim, or exit. End with sizing, book fit, and technical plus fundamental invalidation.
- **ANALYSIS** — whether the security is mispriced. End with cheap/fair/rich, approximate magnitude, and what must be true for the contrary view.
- **BOTH** — address both lenses.

Frame the security, direction, thesis, horizon, relevant catalyst, and risk-free-rate convention. For DECISION or BOTH, also frame intended/current size and risk budget. If an ANALYSIS request lacks direction or horizon, ask one concise question containing only the missing items. If a DECISION or BOTH request lacks direction, horizon, or the risk constraints required for a capital recommendation, ask one concise question containing the missing items and stop.

Before convening the council, inspect any research, trade notes, or portfolio context the user placed in scope. Retrieve only what can change the framed question.

## 2. Build one verified Position Context Block

Create a compact common fact table shared by all advisors. Every current fact includes a source and timestamp or period. Distinguish real-time, delayed, stale, unavailable, and memory-not-verified.

### IBKR market-data workflow

When the Interactive Brokers connector is available, use only these read operations:

1. `search_contracts`
2. `get_price_snapshot`
3. `get_price_history`
4. `get_account_positions` when the portfolio gate below fires
5. `get_account_summary` when the portfolio gate below fires
6. `get_account_balances` when currency normalization is needed

Resolve the instrument with `search_contracts`. Require an exact symbol match and confirm the company, country, primary listing, exchange, and `STK` section. Do not select a leveraged or income exchange-traded fund merely because its symbol resembles the requested equity.

For `get_price_snapshot`, request the useful fields in one call: `last`, `bid_ask`, `prior_close`, `misc_statistics`, `volume`, `implied_volatility_percentile`, `option_open_interest`, `option_volume`, `underlying_avg_option_volume`, `historical_vol`, and `top_status`.

IBKR response keys are hyphenated even when request names use underscores. Read `bid-ask`, `prior-close`, `misc-statistics`, `implied-volatility-percentile`, `option-open-interest`, `option-volume`, `underlying-avg-option-volume`, `historical-vol`, and `top-status`. Treat an empty object as unavailable. Use `misc-statistics.high_52w` and `misc-statistics.low_52w` for the 52-week range; snapshot `high` and `low` are session fields, not the 52-week range. Determine entitlement from `top-status.status` or the response's explicit delayed indicator on every run. Never assume an account is entitled to real-time data.

For history, retrieve daily and weekly open/high/low/close/volume bars with corporate actions included and regular trading hours only. Inspect returned timestamps rather than assuming the requested period was honored. Use the relevant trailing window and note any unexpected coverage. Read the response's `delayed` and `source` fields when present.

Do not force a scalar interpretation onto `implied-volatility-percentile`; report the returned horizons and labels exactly. Compare implied data with `historical-vol.annual_pct` only when both are present and semantically comparable. Never invent options Greeks.

### Portfolio gate

Read the user's IBKR account only when the user explicitly asks to use their holdings, portfolio fit, account capacity, or an existing IBKR position. A hypothetical DECISION/BOTH question does not by itself authorize account retrieval. Do not read the account for a pure ANALYSIS request unless the user explicitly requests portfolio context.

When the gate fires:

- `get_account_positions` supplies holdings, quantities, market values, prices, cost bases, and unrealized profit or loss.
- `get_account_summary` supplies net liquidation value, available funds, buying power, gross position value, leverage, and margin context. Use these to test whether a proposed size is feasible rather than relying on NAV alone.
- Validate that a position's market value and net liquidation value use the same currency before division. When currencies differ, use `get_account_balances` exchange-rate data to normalize them into the account's base currency. State the conversion basis and timestamp. If conversion is unavailable, do not calculate the weight.
- Calculate each relevant holding's portfolio weight only after currency normalization: base-currency market value divided by base-currency net liquidation value.
- If net liquidation value is unavailable, do not treat the sum of positions as NAV. Mark weights unavailable or clearly approximate.
- Identify whether the security is held and surface only holdings relevant to thematic, factor, or drawdown overlap.
- Minimize account data before sharing it with advisors or writing artifacts. Include only the candidate's held/not-held state and weight, directly relevant overlapping holdings and weights, and derived capacity flags. Do not propagate account identifiers, total NAV, raw cash balances, unrelated positions, or exact buying power.
- Fence cost basis and unrealized profit or loss: use them only for a trim/exit realization note and to determine whether a stated stop has already been crossed. Never use embedded gains or losses to justify forward size.

For a trim or exit, state the mechanical realized gain or loss implied by the proposed shares and current price, subject to tax lots and execution. Provide no jurisdiction-specific tax advice.

### Web verification

Use current web research for filings, balance-sheet facts, share count, catalysts, insider transactions, issuer repurchases, short interest, borrow, credit, and material news. Prefer issuer filings, regulator filings, and exchange or other primary sources; use reputable secondary sources where primary data is unavailable. Cite every load-bearing claim.

For long-direction decisions on a single listed common equity, inspect the trailing twelve months of insider transactions and the issuer's repurchase activity. Keep these separate:

- Insider buying is evidence of belief. Evaluate transaction code, open-market versus plan, role, clustering, persistence, timing, size versus the insider's stake or compensation, and size versus market capitalization.
- A buyback or accelerated share repurchase in force is a mechanical bid and may contribute to a structural floor.

The insider row must have one of three states: `not applicable` with reason; `no material signal` with source and searched window; or a sourced finding. Never leave it silent.

For squeeze-prone, heavily shorted, or positioning-driven equities, attempt to verify short interest as a percentage of float, days to cover, and borrow fee or availability. State settlement lag and data limitations. For other equities, provide a number or stamp the assessment as general knowledge not verified to a number.

If IBKR is unavailable, use web sources for price and history where possible and omit account context. If load-bearing market, filing, or catalyst facts cannot be verified, run in degraded mode: mark facts memory-not-verified and avoid false precision. A fully degraded run may analyze the thesis and list missing evidence, but it must return `DEGRADED — NO CAPITAL DECISION`; it must not issue an action, conviction tier, position size, stop, or add level.

### Required Position Context Block rows

- Instrument identity and listing
- Price, bid/ask, prior close, timestamp, and entitlement status
- 52-week range and distance from high/low
- Daily and weekly trend, a defensible named pattern or `no reliable pattern`, support, resistance, volume signature, and technical invalidation
- Options and realized-volatility fields, with unavailable fields shown explicitly
- Short interest and borrow stamp
- Insider-flow and issuer-bid stamp
- Key balance-sheet and catalyst facts
- Held/not-held, current weight, and relevant book overlap when the portfolio gate fires
- Cost-basis realization note only when applicable

## 3. Convene five advisors

The five roles are:

1. **Bear** — full short thesis, failure mechanisms, catalysts, and at least one falsifiable bear claim.
2. **Bull** — long thesis, asymmetric upside, market mispricing, compounding conditions, and at least one falsifiable bull claim.
3. **Base Rate Skeptic** — historical priors, comparable setups, hit rates, and what the story must overcome. Source quantitative base rates or label them as estimates.
4. **Market Structure Analyst** — counterparties, liquidity, positioning, factor crowding, forced flows, options, borrow, insider flow, issuer bid, and technical setup.
5. **Risk Manager** — survival if wrong, size, drawdown, liquidity, portfolio overlap, stop/add zones, and regret minimization.

Every advisor receives only the same framed question and Position Context Block. Each must make at least one quantitative, falsifiable claim, define acronyms on first use, disagree directly when warranted, and distinguish sourced facts from estimates.

The Market Structure Analyst must include two named lines:

- `Insider flow and issuer bid:` finding, evidenced no-signal stamp, or not-applicable reason.
- `Technical levels:` named pattern, support, resistance, and invalidation.

The Risk Manager must consume the shared context directly rather than another advisor's analysis. For DECISION and BOTH, it must tie size to technical and fundamental invalidation.

Target 250–400 words per advisor, with up to 500 for Market Structure and 450 for Risk Management when necessary. Preserve required content over exact word counting; do not pad.

### Codex subagent protocol

When collaboration tools are available, use fresh subagents for the five advisors. This skill explicitly authorizes that scoped delegation. Spawn every advisor with `fork_turns: "none"` or the host's equivalent no-history isolation. Dispatch them in waves that respect the host's concurrency limit. Give each agent only its role, the framed question, and the Position Context Block. Do not reuse an agent for a different advisor role and do not pass any advisor output to another advisor.

If subagents are unavailable, produce role-isolated passes in the primary context and disclose that independence was simulated rather than context-isolated.

## 4. Run five blind peer reviews

Randomize the mapping of advisor outputs to Response A–E. Preserve the mapping privately until presentation.

Each reviewer receives only the framed question, Position Context Block, and all five anonymized responses. It answers:

1. Which response makes the strongest specific falsifiable claim, and what is it?
2. Which response has the largest analytical blind spot, and why?
3. What did all five responses miss?
4. Coverage check: are both mandatory Market Structure stamp lines present and substantive?

Reviews must stand alone: restate a claim before referencing its letter, define acronyms, and explain dossier-only mechanisms. Target 200–325 words.

Use five fresh subagents for reviewers when collaboration tools are available, again in capacity-limited waves. Spawn every reviewer with `fork_turns: "none"` or equivalent isolation and pass the anonymized response bundle explicitly. This scoped delegation is authorized. Each reviewer sees no other review. Do not reuse advisor agents as reviewers. If subagents are unavailable, disclose simulated independence.

Afterward, de-anonymize the displayed reviews by replacing letters with advisor names. Add a concise chief-investment-officer synopsis of repeated blind spots, the strongest common claim, and the unresolved question. It is not a verdict and contains no size or recommendation.

## 5. Draft, challenge, and finalize

### Chairman draft

The chairman receives the named advisor outputs and all peer reviews. It must weigh conflicts rather than average them and run three explicit checks:

1. **Anti-sycophancy** — did the council manufacture skepticism because skepticism sounds rigorous?
2. **Anti-narrative** — did anecdotes displace a quantitative bar or base rate?
3. **Conviction symmetry** — is confidence or caution actually paid for by the evidence?

The internal draft uses this structure:

- `Internal checks run`
- `Where Bull and Bear Converge`
- `The Asymmetry` with bear/base/bull cases and rough probabilities
- `What the Market Structure Changes`
- `The Verdict`
- `The One Trigger That Would Change Your Mind`

The draft is stamped `DRAFT — pending challenge` and is not shown in the final artifacts.

### Challenge round

Run exactly one adversarial challenge on the complete bundle. Prefer a fresh same-model subagent spawned with `fork_turns: "none"`; pass the complete bundle explicitly. Otherwise run an isolated in-context pass. Stamp provenance accurately as `same-model (fresh subagent)` or `same-model (in-context)`. Never call it cross-model unless a genuinely different model performed it.

The challenger audits and never authors. It returns no recommendation, conviction tier, or size. It covers:

1. Unresolved collective misses from peer review
2. Draft claims unsupported by the bundle
3. Conviction in both directions: unearned or under-rated
4. Blind spots shared by the whole council and chair
5. Specific bounded re-tasks routed to named advisors

Permit `nothing material` under a heading; manufacturing criticism is a failure.

### Final chairman verdict

For every challenge point, the chairman must either:

- **Address** it through a bounded advisor addendum,
- **Adjudicate** it from evidence already in the bundle, or
- **Overrule** it with stated reasoning.

The chairman remains responsible for the decision and may reject the challenger. The final verdict uses the draft structure without the draft stamp and adds a `Challenge Round` block listing every point and its resolution.

Use these conviction tiers: `FAT PITCH`, `FAVORABLE`, `FAIR`, `PASS`, `AVOID`.

Award FAT PITCH only when all hold: the upside is supported or downside demonstrably capped; the base rate is supportive; a sourced structural floor exists; skew is positive; and the Risk Manager supports a meaningful survivable size. The structural floor must come from an actual bid, forced or anchored buyer, asset backing, or balance-sheet support. Insider purchases alone do not qualify. When the gate is met, do not withhold the tier merely because caution sounds prudent.

For DECISION or BOTH, state action, size as percentage of NAV, technical invalidation, fundamental invalidation, book fit, and any applicable realization note only when verified facts and adequate risk/capacity information support them. If account access was not authorized or risk capacity is missing, give a conditional sizing range with explicit assumptions or state `size not determined`; do not claim to have assessed book fit. If verified capacity makes the proposed size infeasible, cap it or withhold it. For ANALYSIS or BOTH, state cheap/fair/rich, approximate magnitude, and what must be true for the contrary view.

End with one observable trigger that would change the verdict. Keep it singular even though the verdict may contain both technical and fundamental invalidation.

## 6. Generate artifacts

Create:

- `council-challenge-transcript-[YYYYMMDD-HHMMSS].md` — original and framed questions, sources, Position Context Block, five advisors, five de-anonymized reviews, peer-review synopsis, full challenge, final verdict, and provenance.
- `council-challenge-report-[YYYYMMDD-HHMMSS].html` — a self-contained, accessible report with the verdict prominent, a bear/base/bull payoff bar for DECISION or BOTH, collapsible advisor and review sections, a visible peer-review synopsis, a visible Challenge Round resolution block immediately above the final verdict, and the full challenge in a collapsible section.

Save artifacts to the host's designated user-facing output directory when one exists. Otherwise use an isolated temporary output directory outside any source checkout. Never add council artifacts to an active repository unless the user explicitly chose that destination. If neither destination is available, ask the user where to save them. Open the HTML report in the host's preview panel when supported.

Use the Slate & Ember theme: graphite `#161616`, panels `#202020`, expanded bodies `#262625`, warm text `#E8E6E0`, muted text `#94918A`, low-opacity white hairlines, and restrained ember `#DD8B5A`. Use ember only for outlines and rules. Use Georgia/Cambria for headings and the system sans stack for body text. Keep focus visible and respect `prefers-reduced-motion`.

## Completion checks

Before finishing, confirm only these observable invariants:

- Classification and Position Context Block are present.
- Current facts are sourced and entitlement/delay is stated.
- The portfolio gate was followed, shared account data was minimized, and any portfolio weights were currency-normalized.
- Five independent advisor outputs and five independent reviews are present, with any simulated independence disclosed.
- Insider/issuer and technical stamps are substantive.
- The challenge ran exactly once and never authored the verdict.
- Every challenge point has an addressed, adjudicated, or overruled resolution.
- Both artifacts contain the same final tier, sizing, invalidations, trigger, and provenance, or the same degraded no-decision stamp.
- No IBKR mutation or trading tool was called.
