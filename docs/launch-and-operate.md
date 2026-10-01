# Launch and operate

Everything here is **proposed: not yet exercised by any of the three builds** unless a line says otherwise. It is in the standard because the builds showed where it would be needed.

## 1. Staged rollout

| Step | What happens | Exit criterion |
|---|---|---|
| Shadow | The agent drafts; a person reviews and sends. | Drafts sent unchanged or with minor edits ≥ the resolution-precision threshold, over at least the minimum n from [metrics.md](metrics.md#minimum-sample-size). No safety miss. |
| Limited | The agent handles a fixed share of traffic or one contact type. | Headline metrics from a production sample meet their gates over the minimum n. No rollback trigger fired. |
| Full | All in-scope traffic. | Monitoring (section 5) live; kill switch tested. |

## 2. Rollback triggers

One per headline metric. Defaults are starting points; Design records the agreed version.

| Metric | Trigger | Action |
|---|---|---|
| Fabrication rate | Tier 3: any confirmed fabrication. Tiers 1–2: rate over budget with the interval above it. | Tier 3: kill switch. Tiers 1–2: back one step. |
| Safety-routing recall | Any confirmed miss | Back to shadow; incident review |
| Resolution recall | Interval entirely below the gate | Hold at the current step |
| Handoff precision | Interval entirely below the gate | Hold at the current step |
| Cost per resolution | Above the human baseline for a full week | Hold at the current step |
| Repeat contact, CSAT | Worse than the human baseline beyond the interval | Hold at the current step |
| Escalation rate | Sustained rise against launch week | Review before the next step |

## 3. Kill switch

One action routes every contact to people. The launch owner, the CX operations lead or the on-call engineer can pull it without further sign-off. It is tested in the sandbox before Launch closes, and the test is recorded.

## 4. Integration checks

| Check | How |
|---|---|
| Every tool the agent **and its guardrails** reference exists in the target system | Diff tool names in prompts, skills and guardrail rules against the target API. *Exercised by failure:* [a guardrail matched tool names that did not exist](https://github.com/annagibaeva/Return-and-Exchange-agent/blob/09c28d95f55146c2301754b799f05ee619f542a6/docs/tau2-teardown.md#L83). |
| Auth and permission scopes | Call each tool with the production-scoped credential in the sandbox; the agent cannot reach anything outside scope |
| Rate limits and timeouts | Load the sandbox at expected peak; a timeout produces a handoff, not a claimed success |
| Retries and idempotent writes | Replay each write twice; the second must not create a second record, refund or label |
| Enumerated values | Every status, reason code and field value the agent sends matches what the target system accepts |

## 5. Post-launch monitoring

- **Headline metrics** from a weekly random sample of production contacts, labeled against policy, at least the minimum n.
- **Repeat contact within 7 days** and **CSAT**, compared with the human baseline recorded at Design.
- **Handoff context quality:** a weekly sample of handoffs, checking whether the person had to re-ask for something the customer already gave.
- **Escalation rate** by week.
- Confirmed failures go into the gold set ([gold-set.md](gold-set.md), step 7).

## 6. Change triggers

What re-runs before a change ships.

| Change | Re-run |
|---|---|
| Prompt or skill text | Full eval, pass^k, regression gate |
| Model or model version | Full eval, pass^k at a higher k, cost budget re-derived, regression gate, external test |
| Tool added or changed | Integration checks, state-graded suite, action-claim integrity |
| Policy or knowledge-base rule | Update gold labels first, then affected contact types and rule-conflict cases, regression gate |
| Gate, guardrail or detector code | Detector validation, gate off vs on comparison, full eval |
| Threshold | Written reason; re-judge the latest report (no re-run) |
| New language, customer or system | The full Expand stage |

*Exercised:* the payments harness keys every recorded response on a hash of the configuration, so a changed prompt or model cannot reuse old results ([cassette.py](https://github.com/annagibaeva/payments-harness/blob/f107e59a65b0e803f7e922e0d63ec944a8c26fe0/harness/cassette.py#L9)).
