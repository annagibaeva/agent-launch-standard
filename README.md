# Agent Launch Standard

The checklist I run before any AI agent talks to customers. Five stages (Design, Build, Eval, Launch, Expand), each with exit criteria, the metrics that decide it, the artifact that closes it and the person who signs it off.

**An agent is ready when it clears gates written before it was built, not when it scores well on a test its builder wrote.**

## Who this is for

| File | Reader | Use it to |
|---|---|---|
| This page | Launch owner: whoever makes the ship / no-ship call | Decide |
| [stages.md](docs/stages.md) | Launch owner, engineers | See every criterion, artifact and sign-off |
| [metrics.md](docs/metrics.md) | Everyone | Look up a metric, its formula and how many cases it needs |
| [launch-and-operate.md](docs/launch-and-operate.md) | Launch owner, CX operations, engineers | Plan rollout, rollback and change control |
| [gold-set.md](docs/gold-set.md) | Launch owner, labelers | Build the test set from real data |
| [eval-report-schema.md](docs/eval-report-schema.md) | Engineers | Make the harness emit a checkable report |
| [adopt.md](docs/adopt.md) | Launch owner with the business owner | Fill in the gates for one agent |
| [evidence.md](docs/evidence.md) | Anyone checking a number | Trace it to a file and commit |

## Three rules

**1. Severity budgets.** Tolerance follows damage: the most damaging failure gets zero, an honest miss gets a budget. In [payments-harness](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50), raising temperature from 0 to 1 left accuracy passing at 0.973 while the hallucination gate failed: the assistant made up a fee. A single accuracy score would have shipped it.

**2. Conjunctive gates.** The win condition is an AND of clauses, each chosen to block the cheap way to pass another. Refusing everything fails recall. Answering everything fails hallucination. Escalating everything fails handoff precision. In [wismo-returns](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L196), switching the grounding gate on took hallucination from 7% to 0% and resolution precision from 91% to 100% while recall held at 93%. The price was containment, down from 72% to 66%. That price gets published.

**3. Eval sufficiency.** A perfect score does not close Eval. It means the test is not hard enough yet. [Return-and-Exchange](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L239) passed 10/10 core cases on every one of five runs. On [τ²-bench retail](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L7), a public customer-service agent benchmark that grades the final database state, it passed 59/114 (52%). Aligning its tools and switching off its supervisor layer brought it to 92/114 (81%).

## Five stages

| Stage | Question | Exit gate, short form | Signs off |
|---|---|---|---|
| **Design** | What does failure cost, and what must success beat? | severity budgets · AND win condition · eval mix where refusing everything fails · risk tier · human baseline · acceptance agreed in writing | launch owner, business owner |
| **Build** | Who decides what ships? | model proposes, code decides · policy as data · actions checked against the tool trace · audit trail · personal data blocked | engineering lead |
| **Eval** | Is the test hard enough, and big enough? | win condition with intervals · detectors validated · pass^k above temperature 0 · held-out gap · 100% triggers an external test · an interval across a threshold is directional · second labeler | launch owner, independent labeler |
| **Launch** | Does it hold on the target system, and can we stop it? | regression and cost gates in CI · state-graded acceptance · integration checks · sandbox run · staged rollout · rollback triggers · kill switch | launch owner, engineering lead, CX operations lead |
| **Expand** | Does each new segment re-earn it? | each segment scored on its own · original segment reproduces · budgets re-derived · no rounding at the gate | launch owner |

Tier 3 adds risk or compliance sign-off at Design and Launch. Full criteria, and the artifact that closes each stage: [stages.md](docs/stages.md).

## Headline metrics

Five numbers decide go / no-go and set the rollback triggers:

1. **Fabrication rate** (the source repos call it hallucination): 0 at Tier 3 (see [risk tiers](#risk-tiers)), at most 2% otherwise.
2. **Resolution recall:** at least 80%.
3. **Handoff precision:** at least 85%.
4. **Safety-routing recall:** 100%.
5. **Cost per resolution:** at or below the human baseline.

Containment sits beside them, reported but never gated, so the cost of safety stays visible. Guardrail metrics such as action-claim integrity, pass^k and regression delta also block. Defaults are starting points; Design records the reason for each agent's numbers. Definitions and minimum sample sizes: [metrics.md](docs/metrics.md).

## Risk tiers

Budgets follow what the agent can do, not its domain.

| Tier | The agent can | Fabrication | Claimed actions without a tool call | Safety pass^k | Human approval |
|---|---|---|---|---|---|
| 1 · Inform | answer from read-only sources | ≤2% | n/a | 100% | none |
| 2 · Act | write to systems of record | ≤2% | 0 | 100% | irreversible writes |
| 3 · Regulated | touch money, identity, health or legal status | 0 | 0 | 100%, higher k before each release | refunds, credits, disputes |

Applied after the fact: order tracking is Tier 1, returns Tier 2, and a payments assistant Tier 3: it is read-only, but a fabricated financial fact is a compliance event.

## Scorecard

Exercised criteria met, out of those that apply to each build. Each cell links to the [audit](docs/evidence.md#scorecard-audit).

| | Design | Build | Eval | Launch | Expand |
|---|---|---|---|---|---|
| [payments-harness](https://github.com/annagibaeva/payments-harness) | [4/4](docs/evidence.md#scorecard-audit) | [3/5](docs/evidence.md#scorecard-audit) | [2/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [2/3](docs/evidence.md#scorecard-audit) |
| [wismo-returns](https://github.com/annagibaeva/wismo-returns-reliability-agent) | [4/4](docs/evidence.md#scorecard-audit) | [4/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [1/4](docs/evidence.md#scorecard-audit) | [4/4](docs/evidence.md#scorecard-audit) |
| [Return-and-Exchange](https://github.com/annagibaeva/Return-and-Exchange-agent) | [2/4](docs/evidence.md#scorecard-audit) | [4/5](docs/evidence.md#scorecard-audit) | [2/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [0/4](docs/evidence.md#scorecard-audit) |

No build closes any stage: proposed criteria and sign-offs are unmet everywhere. Eval stays open even on exercised criteria: payments-harness scored accuracy 1.000 on its own tasks and never faced an external test, so Rule 3 holds it open.

Not exercised by any of these builds: human baseline, written acceptance, risk tier, integration checks, sandbox run, staged rollout, rollback triggers, kill switch, monitoring, named sign-off, second labeler, directional labeling, production failures fed back into the gold set. They stay in the standard marked as proposed: the builds showed where they would be needed, and none has exercised them yet.

## Known limits

WISMO's percentages are directional. They show the mechanism works; they do not establish the rates.

- Recall 40/43 (93%): the 95% interval runs 81–97%. The lower bound clears the 80% gate by one point.
- Handoff precision 22/22 (100%): the interval runs 85–100%. The lower bound sits on the gate.
- Hallucination 0/40 (0%): the upper bound is 8.8%, more than four times the 2% gate. Showing at most 2% with zero observed errors needs n ≥ 189.
- At n=43, one ticket moves a rate by 2.3 points.
- English, Spanish and Indonesian are one corpus translated three ways: three times the tickets, not three times the evidence.
- The scores are self-graded by the same system; there is no native-speaker sign-off yet.

Under this standard's own [verdict rule](docs/eval-report-schema.md#verdict-rule), WISMO's win condition is directional, not passed. All three builds use synthetic data, and none has run on production traffic.

## Adopt it

1. Fill in [adopt.md](docs/adopt.md) with the business owner.
2. Build the test set with [gold-set.md](docs/gold-set.md).
3. Make the harness emit the [eval report](docs/eval-report-schema.md).
4. Plan rollout and change control with [launch-and-operate.md](docs/launch-and-operate.md).
