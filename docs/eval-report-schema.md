# Eval report schema

Every harness used under the standard emits one JSON report per run. With a fixed shape, gates can be checked by code instead of read off a dashboard. A checker that enforces this file is planned; for now the contract is documented.

## Top-level fields

| Field | Type | Meaning |
|---|---|---|
| `agent`, `agent_version` | string | What was tested |
| `git_sha` | string | Commit of the agent and harness |
| `model` | string | Model identifier used for the run |
| `config_hash` | string or null | Hash of prompt, model and settings; null if the harness does not compute one |
| `risk_tier` | 1, 2 or 3 | From Design |
| `dataset` | object | `id`, `version`, `frozen_date`, `n`, `splits` |
| `baseline_ref` | string or null | Pinned baseline this run is compared with |
| `generated_at` | ISO 8601 string | When the run finished |
| `metrics` | array | One entry per metric, below |
| `win_condition` | object | `clauses` (metric names) and `verdict` |
| `sufficiency` | object | `internal_perfect` (bool) and `external_test` (`name`, `n`, `result`, or null). `internal_perfect` is true when overall task success on the internal set is at its maximum (every task passes), not when a single clause or an exact gate reads 100%. |
| `labeler_agreement` | number or null | From the gold set; null means self-graded |

## Metric entry

`name` · `family` (headline, guardrail, report) · `type` (gate, budget, report) · `numerator` · `denominator` · `value` · `ci_low` · `ci_high` (95% Wilson, 4 decimals) · `threshold` · `direction` (`>=`, `<=`, `=`) · `verdict`.

## Verdict rule

- `type: report` → `report`. Never blocks.
- `direction: "="` → `pass` if the value equals the threshold, else `fail`. Used for 100% safety routing and zero budgets. The interval is printed but not used, because it can never sit entirely at 100% or 0%.
- `direction: ">="` or `"<="` → `fail` if the value misses the threshold; `pass` if the whole interval is on the passing side; otherwise `directional`.
- Win condition → `fail` if any clause fails; `pass` if every clause passes; otherwise `directional`.
- If `sufficiency.internal_perfect` is true, `external_test` must be filled in before Eval can close.
- Eval closes only if the win condition passes and every guardrail gate or budget passes.

## Example: WISMO, English, live model

Counts and intervals from [evidence.md](evidence.md); model, commit, dates from the WISMO report's run header; `agent_version`, `dataset.id` and `risk_tier` are illustrative. Under this rule the run is **directional**: fabrication rate (hallucination) and silent fact error meet ≤2% on the point estimate, but their upper bounds reach 8.76%. Resolution precision is directional for the same reason against its 95% gate. See [metrics.md](metrics.md#minimum-sample-size) for how many cases would close it.

```json
{
  "agent": "wismo-returns-reliability-agent",
  "agent_version": "v0",
  "git_sha": "8773336c7535ca137c4bf9340fb7c387fb94cf8a",
  "model": "claude-opus-4-8",
  "config_hash": null,
  "risk_tier": 2,
  "dataset": {"id": "wismo-seed-en", "version": "8773336", "frozen_date": "2026-06-22", "n": 65, "splits": {"seed": 65}},
  "baseline_ref": null,
  "generated_at": "2026-09-14T05:45:51Z",
  "metrics": [
    {"name": "fabrication_rate", "family": "headline", "type": "budget", "numerator": 0, "denominator": 40, "value": 0.0, "ci_low": 0.0, "ci_high": 0.0876, "threshold": 0.02, "direction": "<=", "verdict": "directional"},
    {"name": "resolution_recall", "family": "headline", "type": "gate", "numerator": 40, "denominator": 43, "value": 0.9302, "ci_low": 0.8139, "ci_high": 0.976, "threshold": 0.8, "direction": ">=", "verdict": "pass"},
    {"name": "handoff_precision", "family": "headline", "type": "gate", "numerator": 22, "denominator": 22, "value": 1.0, "ci_low": 0.8513, "ci_high": 1.0, "threshold": 0.85, "direction": ">=", "verdict": "pass"},
    {"name": "safety_routing_recall", "family": "headline", "type": "gate", "numerator": 15, "denominator": 15, "value": 1.0, "ci_low": 0.7961, "ci_high": 1.0, "threshold": 1.0, "direction": "=", "verdict": "pass"},
    {"name": "silent_fact_error_rate", "family": "guardrail", "type": "budget", "numerator": 0, "denominator": 40, "value": 0.0, "ci_low": 0.0, "ci_high": 0.0876, "threshold": 0.02, "direction": "<=", "verdict": "directional"},
    {"name": "resolution_precision", "family": "guardrail", "type": "gate", "numerator": 40, "denominator": 40, "value": 1.0, "ci_low": 0.9124, "ci_high": 1.0, "threshold": 0.95, "direction": ">=", "verdict": "directional"},
    {"name": "containment", "family": "report", "type": "report", "numerator": 43, "denominator": 65, "value": 0.6615, "ci_low": 0.5404, "ci_high": 0.7647, "threshold": null, "direction": null, "verdict": "report"}
  ],
  "win_condition": {
    "clauses": ["fabrication_rate", "resolution_recall", "handoff_precision", "silent_fact_error_rate", "safety_routing_recall"],
    "verdict": "directional"
  },
  "sufficiency": {"internal_perfect": false, "external_test": null},
  "labeler_agreement": null
}
```
