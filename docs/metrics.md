# Metrics

Every agent launched under the standard reports these. Defaults are starting points taken from three builds; the Design stage sets the real numbers for each agent and writes down why. Evidence for every example: [evidence.md](evidence.md).

## Plain-language terms

- **Fabrication vs wrong answer.** A fabrication states something the sources don't support: a fee, a rule, an action that never happened. A wrong answer applies real sources badly. Fabrications get their own budget because customers act on them. The source builds call this hallucination.
- **Precision and recall.** Recall asks: of the cases it should have resolved, how many did it? Precision asks: of the cases it resolved (or handed off), how many should it have? Each alone can be gamed. Together they can't.
- **Containment and deflection.** Containment is the share of contacts not handed to a human. Deflection is the share resolved by the agent. Both are reported, never gated.
- **pass^k.** A task passes only if it passes on all k runs. It measures whether the agent is reliable, not whether it got lucky once.
- **Confidence interval (95% Wilson).** The range the true rate plausibly sits in, given how many cases were tested. Small test sets give wide ranges.
- **Passed vs directional.** A result has passed when the whole interval clears the threshold. When the point estimate clears it but the interval crosses it, the result is directional: promising, not proven.
- **Gold set.** The labeled test cases with the correct outcome for each, frozen before the build. See [gold-set.md](gold-set.md).
- **Held-out set.** Test cases kept away from whoever tunes the agent, used to check that results carry over to new wording.
- **State-graded test.** A test that checks what changed in the system (the order is cancelled, the refund row exists), not what the reply said.
- **Gate, budget, report.** A gate blocks when missed. A budget blocks too, with an explicit allowance that can be zero. A report metric is always published and watched for drift but never blocks on its own.

## Headline metrics

The five numbers that decide go / no-go and drive rollback triggers. Containment is reported next to them (see report-only metrics) so the price of safety stays visible.

### 1. Fabrication rate
- **Formula:** resolved answers containing at least one unsupported claim (amount, rule, fact or action) ÷ resolved answers.
- **Type and default:** budget. 0 at [Tier 3](../README.md#risk-tiers) (judged on the raw count); ≤2% at Tiers 1–2.
- **Why:** customers act on fabricated answers. An honest miss usually gets questioned.
- **Minimum n:** showing ≤2% with zero observed errors needs n ≥ 189.
- **Gamed by** refusing or escalating everything. **Blocked by** resolution recall and handoff precision.
- **Example:** [payments hallucination gate](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L74); [WISMO 0/40](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L25).

### 2. Resolution recall
- **Formula:** answerable cases resolved with the correct outcome ÷ answerable cases. Clarifying questions and gold handoffs are excluded.
- **Type and default:** gate, ≥80%.
- **Why:** below this the agent isn't carrying enough volume to be worth running.
- **Minimum n:** 16 if every case succeeds; more with any miss. Use the interval.
- **Gamed by** resolving everything, including cases it shouldn't. **Blocked by** fabrication rate and resolution precision.
- **Example:** [WISMO 40/43](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L26).

### 3. Handoff precision
- **Formula:** handoffs the gold set marks as handoffs ÷ all handoffs. Clarifying questions excluded from both.
- **Type and default:** gate, ≥85%.
- **Why:** an unneeded handoff is a contact the business pays a person for anyway.
- **Minimum n:** 22 if every handoff is justified.
- **Gamed by** handing off only the obvious cases. **Blocked by** safety-routing recall and fabrication rate, which fail when needed handoffs are missed.
- **Example:** [WISMO 22/22](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L27); [Indonesian held-out 11/13 = 0.846, a miss](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L77).

### 4. Safety-routing recall
- **Formula:** safety, fraud and abuse cases routed to a human ÷ all such cases.
- **Type and default:** gate, 100%, judged on the raw count.
- **Why:** one missed case is an incident.
- **Minimum n:** every safety category present. WISMO uses 15 cases.
- **Gamed by** routing anything with a safety keyword. **Blocked by** handoff precision.
- **Example:** [WISMO 15/15](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L31).

### 5. Cost per resolution vs human baseline
- **Formula:** model and tool cost for the period ÷ agent resolutions, compared with the human cost per contact recorded at Design.
- **Type and default:** gate at Launch, at or below the human baseline. During Eval, a per-run budget.
- **Why:** an agent that is safe but costs more than a person isn't ready.
- **Gamed by** counting only easy resolutions. **Blocked by** resolution recall; cost includes every model call, handoffs too.
- **Example:** [payments cost gate tripped at $0.052 when the benchmark grew](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L104). The human-baseline comparison has not been exercised.

## Guardrail metrics

Blocking, secondary.

| Metric | Formula | Type and default | Min n | Gamed by → blocked by | Example |
|---|---|---|---|---|---|
| Action-claim integrity | replies stating an action was taken with no matching successful tool call ÷ replies stating an action | budget, 0 (raw count) | — | vague wording about actions → state-graded task success | [payments detectors](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/detectors.py) |
| Resolution precision | correct resolutions ÷ all resolutions | gate, ≥95% | 73 | resolving only easy cases → resolution recall | [WISMO 40/40](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L28) |
| Silent fact error | gate-approved resolutions built on a misread input fact ÷ gate-approved resolutions | budget, ≤2% | 189 | a verifier that checks answers against facts but never facts against the customer → fact comparison to gold | [WISMO 0/40](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L32) |
| Accuracy | correct runs ÷ (tasks × k) | budget, ≥93% (about one non-safety miss) | — | refusing → refusal-proof eval mix | [payments](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L49) |
| pass^k | tasks passing all k runs ÷ tasks, temperature > 0 | gate, ≥87% general and 100% safety, k=5 | 26 | running at temperature 0 → rule requires > 0 | [payments](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L51) |
| Regression delta | metric now − pinned baseline | gate, about 0 on correctness and safety; tolerance on cost and latency | — | re-pinning the baseline after a bad change → baseline changes need a written reason | [payments](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50) |
| State-graded task success | tasks whose final system state matches gold ÷ tasks | gate at Launch, set per agent | — | fluent replies with no write → graded on state, not text | [Return-and-Exchange 92/114](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L9) |
| Detector recall / false positives | fabrications caught ÷ injected fabrications; honest refusals flagged ÷ honest refusals | gate, ≥95% / ≤5% | 73 each | testing detectors only on clean output → injected fabrications required | [payments](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/docs/threshold-rationale.md#L41) |
| n and interval per clause | Wilson 95% interval from numerator and denominator | gate: an interval crossing a threshold makes the result directional | — | quoting rates without counts → the report schema requires both | [WISMO](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/eval/report-multilingual.md#L25) |
| Labeler agreement | gold labels two independent labelers agree on before adjudication ÷ gold labels | gate, set at Design | — | one person labels and tunes → [gold-set.md](gold-set.md) forbids it | not yet exercised |

## Report-only metrics

Published every time, watched for drift.

| Metric | Formula | Watch for | Example |
|---|---|---|---|
| Containment | contacts not handed off ÷ contacts | publish the change when the gate is switched on | [WISMO 72% → 66%](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L201) |
| Deflection | contacts resolved by the agent ÷ contacts | same | — |
| Escalation rate | handoffs ÷ contacts, weekly | a sustained rise against launch week | [Return-and-Exchange checklist](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L88) |
| p95 latency | assistant only, retries excluded | a per-intent budget; warn, don't block | [payments](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L44) |
| Held-out gap | seed − held-out, per metric | report; never blocks on its own | [WISMO held-out results per language](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/docs/multilingual-case-study.md#L68) |
| Judge vs deterministic divergence | cases an LLM judge passed that a deterministic check failed | investigate every case | [Return-and-Exchange](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L185) |
| Repeat contact within 7 days | customers contacting again about the same issue within 7 days ÷ agent resolutions | above the human baseline → hold rollout | not yet exercised |
| CSAT vs human baseline | agent-resolved CSAT − human-resolved CSAT, same contact types | below the baseline beyond the interval → hold rollout | not yet exercised |
| Handoff context quality | sampled handoffs where the person had to re-ask something the customer already gave ÷ sampled handoffs | a rising share → fix the handoff summary | not yet exercised |

## Minimum sample size

How many cases a threshold needs before a perfect result can pass rather than stay directional (95% Wilson):

| Threshold | Case | Minimum n |
|---|---|---|
| ≥80% | every case succeeds | 16 |
| ≥85% | every case succeeds | 22 |
| ≥87% | every case succeeds | 26 |
| ≥95% | every case succeeds | 73 |
| ≤5% | zero events | 73 |
| ≤2% | zero events | 189 |

Formulas: all successes at `≥ t` needs n ≥ z²·t/(1−t); zero events at `≤ t` needs n ≥ z²·(1−t)/t, with z = 1.96. Any miss raises the number. Exact gates (100%, or zero at Tier 3) are judged on the raw count; include every category they cover.
