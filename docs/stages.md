# Stages

Five stages. A stage closes when every criterion holds, its artifact exists and is linked, and its owner has signed. **E** = exercised in at least one of the three builds (linked). **E (partial)** = exercised in part. **P** = proposed, not yet exercised by any build.

Metric definitions: [metrics.md](metrics.md). Numbers: [evidence.md](evidence.md).

## Design — what does failure cost, and what does success have to beat?

| # | Criterion | Tag | Worked example or detail |
|---|---|---|---|
| a | Failure modes sorted by severity (fabricated, wrong, unhelpful), each with an explicit budget | E | [payments: zero fabrications, one honest miss allowed](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L74) |
| b | Win condition written as AND-clauses before any code | E | [WISMO five-clause condition](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L34) |
| c | Handoff and refusal count as valid outcomes, not failures | E | [WISMO resolve / ask / handoff](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L100) |
| d | Eval mix set so that refusing everything fails | E | [payments 10 answer / 9 refuse: blanket refusal scores 9/19 ≈ 47%](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L39) |
| e | Risk tier assigned ([README](../README.md#risk-tiers)) | P | — |
| f | Human baseline recorded: cost per contact, resolution time, CSAT, repeat contact for the in-scope contact types | P | — |
| g | Acceptance criteria agreed in writing with the business owner: win condition, thresholds, tier, rollback triggers | P | — |

**Closed by:** severity table with budgets, win condition, risk tier, eval-mix spec, human baseline, signed acceptance criteria.
**Signs off:** launch owner and business owner; risk or compliance at Tier 3.

## Build — who decides what ships?

| # | Criterion | Tag | Worked example or detail |
|---|---|---|---|
| a | The model proposes; deterministic code decides. No model on the gate path | E | [WISMO grounding gate: can block, cannot answer](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/gate/gate.py) |
| b | Policy stored as data, each rule carrying its source text | E | [WISMO rules.json](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/kb/rules.json) |
| c | Actions checked against the tool trace, never taken from what the reply claims | E | [Return-and-Exchange action scoring](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/evals/run_evals.py#L127) |
| d | Every decision written to an audit trail | E | [WISMO audit trail](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L98) |
| e | Personal data blocked at the tool layer and redacted from traces and logs | E (partial) | [Return-and-Exchange redacts unless the session email matches](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/outputs.md#L38) |

**Closed by:** gate spec (what it can block, what it can never do), policy file, audit-trail schema.
**Signs off:** engineering lead.

## Eval — is the test hard enough, and big enough?

| # | Criterion | Tag | Worked example or detail |
|---|---|---|---|
| a | Every win-condition clause reported with raw counts and a 95% interval | E | [WISMO report](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L25) |
| b | Fabrication detectors validated on injected fabrications (≥95% recall, ≤5% false positives) | E | [payments detector validation](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L41) |
| c | pass^k at temperature above 0; safety tasks 100% | E | [Return-and-Exchange 10/10 pass^5 at temperature 1.0](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L19) |
| d | Held-out gap reported | E | [WISMO held-out results per language](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L68) |
| e | **Sufficiency:** a 100% result (every internal task passing) sends the agent to a harder external, state-graded test before Eval closes | E | [Return-and-Exchange: 10/10 internally, 59/114 (52%) on τ²-bench retail](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L7) |
| f | **Sample size:** an interval that crosses a threshold makes the result directional, not passed | P | — |
| g | Gold set built per [gold-set.md](gold-set.md), with a second independent labeler and agreement reported | P | — |

**Closed by:** eval report in the [schema](eval-report-schema.md), detector validation, held-out gap, external result when the internal score is 100%, labeler agreement.
**Signs off:** launch owner; the independent labeler signs the gold set.

## Launch — does it hold on the target system, and can we stop it?

| # | Criterion | Tag | Worked example or detail |
|---|---|---|---|
| a | Regression gate against a pinned baseline, blocking in CI | E | [payments: accuracy passed at 0.973, the release still failed](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) |
| b | Cost gate blocking, computed from token usage | E | [payments cost gate](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104) |
| c | Acceptance test grades system state, not reply quality | E | [τ²-bench retail grades the database](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L23) |
| d | Every tool the agent and its guardrails reference exists in the target system | E | [a guardrail matched missing tool names](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L83) |
| e | Integration checks pass: auth scopes, rate limits, timeouts, retries, idempotent writes | P | [launch-and-operate.md](launch-and-operate.md) |
| f | Full suite re-run in the target system's sandbox | P | — |
| g | Staged rollout with an exit criterion per step: shadow, limited, full | P | [launch-and-operate.md](launch-and-operate.md) |
| h | A rollback trigger for each headline metric, and a tested kill switch | P | [launch-and-operate.md](launch-and-operate.md) |
| i | Monitoring live: headline metrics, repeat contact, CSAT against baseline, handoff context quality, escalation drift | P | [launch-and-operate.md](launch-and-operate.md) |
| j | Named sign-off recorded | P | — |

**Closed by:** pinned baseline, CI gate config, integration-check log, sandbox run, rollout plan with rollback triggers, kill-switch test.
**Signs off:** launch owner, engineering lead and CX operations lead; risk or compliance at Tier 3.

## Expand — does each new segment re-earn it?

| # | Criterion | Tag | Worked example or detail |
|---|---|---|---|
| a | Each new language, domain, customer or system is scored on its own against the full win condition | E | [WISMO English, Spanish, Indonesian each scored separately](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L34) |
| b | The original segment reproduces its published numbers exactly | E | [WISMO English baseline test](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/tests/test_regression_baseline.py) |
| c | Budgets re-derived when the benchmark grows | E | [payments cost gate tripped at $0.052, budget re-set](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104) |
| d | **No rounding at the gate:** 0.846 is not 0.85 | E | [WISMO Indonesian held-out 11/13 reported as a miss](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L77) |
| e | Production failures added to the gold set before the next segment | P | [gold-set.md](gold-set.md) |

**Closed by:** per-segment eval report, regression proof, re-derived budgets.
**Signs off:** launch owner.

## When something changes after launch

See the change-trigger table in [launch-and-operate.md](launch-and-operate.md#6-change-triggers).
