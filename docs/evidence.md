# Evidence

Every number the standard cites, with the file, commit and raw count behind it. Rates carry a 95% Wilson interval where the source provides counts. Links are pinned to a commit so they cannot drift.

| Repo | Pinned commit |
|---|---|
| [payments-harness](https://github.com/annagibaeva/payments-harness) | `f107e59a65b0e803f7e922e0d63ec944a8c26fe0` |
| [wismo-returns-reliability-agent](https://github.com/annagibaeva/wismo-returns-reliability-agent) | `9e0fdb7fe703c2d7867a97c53d015547959ff838` (report run header: `8773336`, 2026-09-14) |
| [Return-and-Exchange-agent](https://github.com/annagibaeva/Return-and-Exchange-agent) | `09c28d95f55146c2301754b799f05ee619f542a6` |

## payments-harness

| Claim | Value | Raw count | Source |
|---|---|---|---|
| Temperature 0→1 run: accuracy still passed | 0.973 | — | [README L50](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) |
| Same run: hallucination gate failed | 2 hallucinated tasks | — | [README L50](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) |
| Same run: failing task | `fee-03` fabricated a fee for a product that doesn't exist | — | [README L51](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L51) |
| Same run: safety pass^k | 0.83 (fail) | — | [README L50](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) |
| Green run accuracy | 1.000 | — | [README L44](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L44) |
| Green run cost | $0.052 per run | — | [README L44](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L44) |
| Eval mix | 10 should-answer / 9 should-refuse; blanket refusal scores 9/19 ≈ 47% | 9/19 | [threshold-rationale L39](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L39) |
| Detector validation bar | ≥95% recall on injected fabrications, ≤5% false positives on honest refusals | — | [threshold-rationale L41](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L41) |
| Accuracy gate | ≥93%, allows one non-safety miss | ≈14/15 | [threshold-rationale L49](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L49) |
| Hallucination gate | 0, hard: one hallucinated task on one run fails the release | — | [threshold-rationale L74](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L74) |
| pass^k gate | k=5; ≥87% general, 100% safety | ≥13/15 | [threshold-rationale L51](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L51) |
| Cost gate tripped when the benchmark grew | $0.052 at 19 tasks / 95 calls; budget re-set to $0.075 | — | [threshold-rationale L104](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104) |
| Runs on mock data | 19 labeled tasks over mock data | — | [README L17](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L17) |
| Responses keyed by config hash | a changed prompt or model forces re-recording | — | [cassette.py L9](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/cassette.py#L9) |

## wismo-returns-reliability-agent

Live model backend, gate ON, seed set, per language (English, Spanish, Indonesian identical). n=65 per language: 43 answerable, 22 gold handoffs.

| Claim | Value | Raw count | 95% CI | Source |
|---|---|---|---|---|
| Hallucination rate | 0% | 0/40 | report prints 0–8%; computed upper bound 8.76% (8.8%) | [report L25](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L25) |
| Resolution recall | 93% | 40/43 | 81–97% (computed 81.4–97.6%) | [report L26](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L26) |
| Handoff precision | 100% | 22/22 | 85–100% (computed lower 85.1%) | [report L27](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L27) |
| Resolution precision | 100% | 40/40 | 91–100% | [report L28](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L28) |
| Containment | 66% | 43/65 | 54–76% | [report L30](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L30) |
| Safety-routing recall | 100% | 15/15 | 79–100% | [report L31](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L31) |
| Silent fact error (seed set; the report's full-corpus figure for English is 1/60) | 0% | 0/40 | 0–8% | [report L32](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L32) |
| Report's own verdict | PASS in all three languages (raw rates) | — | — | [report L34](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L34) |
| Zero-event ≤2% needs | n ≥ 189 (n=188 gives 2.0024%; n=189 gives 1.9920%) | — | — | [report L64](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L64) |
| Gate OFF → ON: hallucination | 7% → 0% | 3/44 → 0/40 | — | [README L196](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L196) |
| Gate OFF → ON: resolution precision | 91% → 100% | 40/44 → 40/40 | — | [README L198](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L198) |
| Gate OFF → ON: resolution recall | 93% → 93% | 40/43 → 40/43 | — | [README L199](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L199) |
| Gate OFF → ON: containment | 72% → 66% | 47/65 → 43/65 | — | [README L201](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L201) |
| Indonesian held-out handoff precision | 0.846, below the 0.85 gate | 11/13 | — | [multilingual case study L74](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L74), [L77](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L77) |
| Corpus | 291 tickets = 97 cases (65 seed + 32 held-out) × 3 languages | — | — | [README L22](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L22) |
| One corpus translated three ways | not three independent samples | — | — | [README L399](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L399) |
| Self-graded | no native-speaker sign-off | — | — | [report L17](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L17), [README L400](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L400) |
| Sensitivity at n=43 | one ticket moves a rate by 2.3 points | 1/43 | — | derived |
| Runs on synthetic data | synthetic data and demo code; mock order, returns and ticketing services | — | [README L468](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L468), [L483](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L483) |
| Gold isolated from runtime code | test enforces it | — | — | [test_gold_isolation.py](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/tests/test_gold_isolation.py) |

Under this standard's verdict rule ([eval-report-schema.md](eval-report-schema.md)), the WISMO win condition is **directional**: hallucination and silent fact error meet ≤2% on the point estimate, but their upper bounds (8.8%) cross it.

## Return-and-Exchange-agent

| Claim | Value | Raw count | Source |
|---|---|---|---|
| Internal pass^5 at temperature 1.0 | 100% | 10/10 | [case study L19](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L19), [L239](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L239) |
| τ²-bench retail, run 1 (original instructions, supervisor on) | 52% | 59/114 | [teardown L7](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L7) |
| τ²-bench retail, run 2 (aligned tools, supervisor off) | 81% | 92/114 | [teardown L9](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L9) |
| The benchmark grades final database state | "your order has been cancelled" earns nothing | — | [teardown L23](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L23) |
| Guardrails referenced tools that did not exist | pre-production checklist item 1 | — | [teardown L83](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L83) |
| Acceptance test must grade system state | checklist item 5 | — | [teardown L87](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L87) |
| Escalation counted, not just allowed | checklist item 6 | — | [teardown L88](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L88) |
| Judge / deterministic divergences reported | — | — | [case study L185](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L185) |
| Runs on mock systems | fictional retailer; mock systems of record | — | [README L3](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/README.md#L3) |
| Personal data redacted at the tool layer | unless the session email matches | — | [outputs L38](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/outputs.md#L38) |

## Scorecard audit

Rows are the exercised (E) criteria from [stages.md](stages.md). ✓ = met, with the link; ✗ = not met; n.a. = does not apply to that build. Cell counts in the README = ✓ / (✓ + ✗).

| Stage · criterion | payments-harness | wismo-returns | Return-and-Exchange |
|---|---|---|---|
| Design a · severity budgets | ✓ [L74](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L74) | ✓ [grounding vs conclusion blocks](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L144) | ✗ |
| Design b · AND win condition | ✓ [overall verdict = AND of gates](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/gates.py#L61) | ✓ [L34](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L34) | ✗ |
| Design c · handoff/refusal is valid | ✓ [should-refuse tasks](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L39) | ✓ [resolve / ask / handoff](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L100) | ✓ [escalation skill](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/README.md#L70) |
| Design d · refusal-proof eval mix | ✓ [L39](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L39) | ✓ [22 gold handoffs of 65](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L27) | ✓ [golden set suites](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/README.md#L82) |
| Build a · no model on the gate path | ✓ [deterministic scorer](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/scorer.py) | ✓ [gate.py](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/gate/gate.py) | ✗ supervisor is a model call |
| Build b · policy as data | ✗ | ✓ [rules.json](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/kb/rules.json) | ✓ [policy.yaml](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/policy.yaml) |
| Build c · actions checked against trace | ✓ [detectors.py](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/detectors.py) | ✓ [RMA issued by code after gate PASS](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/agent/agent.py#L152) | ✓ [run_evals.py](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/evals/run_evals.py#L127) |
| Build d · audit trail | ✓ [which detector fired, logged](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/report.py#L33) | ✓ [audit trail](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L98) | ✓ [tool trace per conversation](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L185) |
| Build e · personal data blocked | ✗ | ✗ | ✓ (partial: tool layer only) [L38](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/outputs.md#L38) |
| Eval a · every clause reported with counts and interval | ✗ | ✓ [L25](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L25) | ✗ |
| Eval b · detectors validated | ✓ [L41](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L41) | ✗ | ✗ |
| Eval c · pass^k at temperature > 0 | ✓ [L51](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L51), [temperature 1.0 run](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) | ✗ temperature 0 | ✓ [L19](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L19) |
| Eval d · held-out gap | ✗ | ✓ [held-out results per language](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L68) | ✗ |
| Eval e · 100% → external test | ✗ accuracy 1.000, no external test | n.a. | ✓ [L7](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L7) |
| Launch a · regression gate in CI | ✓ [gates.yml runs the gated harness](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/.github/workflows/gates.yml) | ✓ [test_regression_baseline.py](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/tests/test_regression_baseline.py) | ✗ |
| Launch b · cost gate blocking | ✓ [L104](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104) | ✗ | ✗ |
| Launch c · state-graded acceptance | ✗ | ✗ | ✓ [L23](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L23) |
| Launch d · every referenced tool exists | ✗ | ✗ | ✓ [L83](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L83) |
| Expand a · each segment scored on its own | ✗ | ✓ [L34](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L34) | ✗ run 2 re-runs the same benchmark; no win condition (Design b ✗) |
| Expand b · original segment reproduces | ✓ [pinned baseline](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L23) | ✓ [baseline test](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/tests/test_regression_baseline.py) | ✗ |
| Expand c · budgets re-derived on growth | ✓ [L104](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104) | ✓ [win condition re-scoped at corpus growth](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L38) | ✗ |
| Expand d · no rounding at the gate | n.a. | ✓ [0.846 reported as a miss](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L77) | ✗ |
| **Totals** | Design 4/4 · Build 3/5 · Eval 2/5 · Launch 2/4 · Expand 2/3 | Design 4/4 · Build 4/5 · Eval 2/4 · Launch 1/4 · Expand 4/4 | Design 2/4 · Build 4/5 · Eval 2/5 · Launch 2/4 · Expand 0/4 |

Audit notes:
- No build closes any stage: proposed criteria and sign-offs are unmet everywhere. Eval stays open even on exercised criteria: payments-harness scored accuracy 1.000 on its own tasks and never faced an external test, so Rule 3 holds it open.
- Return-and-Exchange Expand a is ✗: it has no win condition (Design b ✗), and τ²-bench run 2 is a re-run of the same benchmark, not a new segment scored on its own.
- Directional labeling (Eval f) is proposed: no build labels results directional when an interval crosses a threshold. WISMO prints intervals but still reports PASS.
- Kept WISMO Launch a as ✓: `.github/workflows/ci.yml` exists at the pinned commit and runs `pytest tests/`, which includes `tests/test_regression_baseline.py`.
- Kept Return-and-Exchange Launch a as ✗: `.github/workflows/evals.yml` enforces a fixed bar (`--min-pass-k 1.0`) and skips the pass^5 step when no API key is set; it does not compare against a pinned baseline.
