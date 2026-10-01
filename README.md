# Agent Launch Standard

The checklist I run before any AI agent talks to customers. Five stages, each saying what must be true before the agent moves on: the numbers to hit, the document that proves it, and who signs off.

**An agent is ready when it passes tests written before it was built. A high score on a test its builder wrote afterwards doesn't count.**

One-page overview: [Agent Launch Standard at a glance](https://claude.ai/artifact/ANJZa9WRhP1KotKTaofWp6).

## Who this is for

For agent product managers, agent strategists, AI and agent engineers, and forward-deployed engineers. The **launch owner** makes the final ship / don't-ship call: usually the agent's product manager, or the forward-deployed engineer on a customer deployment.

| File | Use it to |
|---|---|
| This page | Decide whether an agent is ready |
| [stages.md](docs/stages.md) | See every check, the document that proves it and who signs |
| [metrics.md](docs/metrics.md) | Look up a metric, how to calculate it and how many test cases it needs |
| [launch-and-operate.md](docs/launch-and-operate.md) | Plan the rollout, the rollback and what to re-test after a change |
| [gold-set.md](docs/gold-set.md) | Build the test set from real customer conversations |
| [eval-report-schema.md](docs/eval-report-schema.md) | Make your test harness produce a report the checks can read |
| [adopt.md](docs/adopt.md) | Fill in the checks for one agent with its business owner |
| [evidence.md](docs/evidence.md) | Trace any number here to the file and commit it came from |

## Three rules

**1. Give each kind of mistake its own limit.** Before testing, sort the mistakes the agent could make by the damage they do. Mistakes a customer acts on, like an invented fee or a refund that never happened, get a limit of zero. Honest misses, like a vague answer, get a small allowance. In [payments-harness](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/README.md#L50), raising the model's temperature from 0 to 1 kept accuracy above its bar at 0.973 while the assistant started inventing a fee. An accuracy score alone would have shipped it; the zero limit on invented facts blocked it.

**2. Require several metrics to pass at the same time.** Any single metric can be gamed. An agent that refuses everything never states a wrong fact, and one that hands everything to a human never gets anything wrong. So pick a few metrics that pull against each other and require all to pass in one test run: the docs call this the *win condition*. In [wismo-returns](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/README.md#L196), adding a check of every answer against written policy cut hallucination from 7% to 0% and raised correct answers from 91% to 100%, while the agent still resolved 93% of the tickets it should. The cost, published with the gains: conversations closed without a human fell from 72% to 66%.

**3. A perfect score means the test is too easy.** If an agent passes every case you wrote, run it on a harder test you didn't write, ideally one that checks what changed in the system rather than what the agent said. [Return-and-Exchange](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/case-study.md#L239) passed 10/10 of my cases on all five runs. On [τ²-bench retail](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L7), a public customer-service agent benchmark graded on the final database, it passed 59/114 (52%). Fixing its tool names and switching off its supervisor layer brought it to 92/114 (81%).

## Five stages

| Stage | The question | What must be true to move on | Who signs off |
|---|---|---|---|
| **Design** | What does a mistake cost, and what must the agent beat? | a limit for each kind of mistake · the metrics that must pass together, written down before building · a test set where refusing everything fails · how much the agent may do (risk tier) · today's human numbers · targets agreed in writing | launch owner, business owner |
| **Build** | Who decides what reaches the customer? | the model suggests, plain code approves or blocks · business rules stored as data, each with its source sentence · claimed actions checked against the tool log · every decision logged · personal data blocked | engineering lead |
| **Eval** | Is the test hard enough, and big enough? | every metric shown with its error range · the checks that catch invented facts tested on planted examples · repeated runs above temperature 0 · results on cases never used for tuning · a perfect score triggers an outside test · a result whose error range crosses the target counts as not proven yet · a second person labels the test set | launch owner, independent labeler |
| **Launch** | Does it work on the real system, and can we switch it off? | nothing worse than the last release, checked automatically · cost per run capped · pass judged on what changed in the system · logins, rate limits and retries tested · a dry run in the customer's test environment · gradual rollout with rollback triggers · a tested off switch | launch owner, engineering lead, CX operations lead |
| **Expand** | Does each new language, customer or system pass on its own? | each new segment tested separately · the original segment gives the same numbers · limits recalculated when the test set grows · no rounding up: 0.846 does not pass an 85% target | launch owner |

Tier 3 agents also need risk or compliance sign-off. Every check: [stages.md](docs/stages.md).

## Headline metrics

Five numbers decide go / no-go and when to roll back:

1. **Invented facts** (fabrication rate; the source repos call it hallucination): 0 for high-risk agents (see [risk tiers](#risk-tiers)), at most 2% otherwise.
2. **Resolution recall**, the share of answerable questions the agent resolves correctly: at least 80%.
3. **Handoff precision**, the share of handoffs to a human that were needed: at least 85%.
4. **Safety routing**, safety, fraud and abuse cases sent to a human: 100%.
5. **Cost per resolved conversation:** at or below what a human costs today.

Containment (conversations closed without a human) is reported beside these but never gated, so the cost of safety stays visible. Other checks also block, such as claiming an action the agent never took. Targets are starting points; Design records each agent's reasons. Definitions and how many test cases each needs: [metrics.md](docs/metrics.md).

## Risk tiers

The limits follow what the agent is allowed to do, not its industry.

| Tier | The agent can | Invented facts | Claimed actions with no tool call | Repeated-run pass rate on safety cases | Human approval for |
|---|---|---|---|---|---|
| 1 · Inform | answer from read-only sources | ≤2% | n/a | 100% | nothing |
| 2 · Act | write to systems of record | ≤2% | 0 | 100% | irreversible writes |
| 3 · Regulated | touch money, identity, health or legal status | 0 | 0 | 100%, with more runs before each release | refunds, credits, disputes |

Applied after the fact: order tracking is Tier 1, returns Tier 2. A read-only payments assistant is still Tier 3, because a made-up financial fact is a compliance event.

## Scorecard

Checks met, out of the tried checks that apply to each of my agents. Cells link to the [audit](docs/evidence.md#scorecard-audit).

| | Design | Build | Eval | Launch | Expand |
|---|---|---|---|---|---|
| [payments-harness](https://github.com/annagibaeva/payments-harness) | [4/4](docs/evidence.md#scorecard-audit) | [3/5](docs/evidence.md#scorecard-audit) | [2/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [2/3](docs/evidence.md#scorecard-audit) |
| [wismo-returns](https://github.com/annagibaeva/wismo-returns-reliability-agent) | [4/4](docs/evidence.md#scorecard-audit) | [4/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [1/4](docs/evidence.md#scorecard-audit) | [4/4](docs/evidence.md#scorecard-audit) |
| [Return-and-Exchange](https://github.com/annagibaeva/Return-and-Exchange-agent) | [2/4](docs/evidence.md#scorecard-audit) | [4/5](docs/evidence.md#scorecard-audit) | [2/5](docs/evidence.md#scorecard-audit) | [2/4](docs/evidence.md#scorecard-audit) | [0/4](docs/evidence.md#scorecard-audit) |

No agent passes a whole stage yet. payments-harness even scored accuracy 1.000 on its own tasks without an outside test, so Rule 3 keeps its Eval open.

About a dozen checks, including the human baseline, rollout and rollback, the off switch and a second labeler, haven't been tried by any build. They stay in, marked proposed in [stages.md](docs/stages.md). τ²-bench's integration failures are why the integration tests exist; WISMO grading its own answers is why the second labeler does.

## Known limits

WISMO's results are directional: promising, but the test set is too small to prove the rates.

- Recall 40/43 (93%): the 95% error range runs 81–97%, so its low end clears the 80% target by one point.
- Handoff precision 22/22 (100%): the range runs 85–100%, and its low end sits on the target.
- Hallucination 0/40 (0%): the range runs up to 8.8%, more than four times the 2% target. Showing at most 2% with zero errors needs n ≥ 189 test cases.
- With 43 answerable tickets, one ticket moves a rate by 2.3 points.
- English, Spanish and Indonesian are one set of tickets translated three ways: three times the tickets, not three times the evidence.
- The system graded its own answers; no native speaker has checked them.

Under this standard's own [verdict rule](docs/eval-report-schema.md#verdict-rule), WISMO's result is directional, not a pass. All three agents run on synthetic data, and none has served real customers.

## Adopt it

1. Fill in [adopt.md](docs/adopt.md) with the business owner.
2. Build the test set with [gold-set.md](docs/gold-set.md).
3. Make the harness emit the [eval report](docs/eval-report-schema.md).
4. Plan rollout and change control with [launch-and-operate.md](docs/launch-and-operate.md).
