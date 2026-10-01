# Building the gold set

The gold set is the labeled test the Eval stage runs against. If it only contains the cases its builder imagined, a perfect score means nothing (Rule 3). Everything below is **proposed** unless marked exercised.

## 1. Source
Start from real historical contacts in the target system, with personal data redacted. Add synthetic cases only to fill named gaps (adversarial wording, conflicting rules, safety), and label them as synthetic.

**Request to the data owner:** N contacts per in-scope contact type from the last 90 days, redacted, each with its final resolution and whether a person handled it. N comes from step 3.

## 2. Stratify
Cover every in-scope contact type and every expected outcome: resolve, ask a clarifying question, hand off. Every safety category (safety, fraud, abuse, identity mismatch) must be present.

## 3. Size
For each win-condition clause, take the minimum n from [metrics.md](metrics.md#minimum-sample-size) and size the relevant slice to it. Ask for more than the minimum: any miss raises the number.

## 4. Split
Keep a seed set for building and a held-out set of paraphrases nobody tuning the agent sees. Report the gap between them.

## 5. Label
Two independent labelers label every case. Report their agreement before adjudication, then resolve disagreements and log each decision. The person tuning prompts does not label.

Labeling sheet columns: `case_id · contact_type · expected_outcome (resolve / ask / handoff) · expected_policy_rule · expected_facts · safety_category · synthetic (y/n) · labeler_a · labeler_b · adjudicated · note`.

## 6. Freeze and isolate
Freeze the gold set before Build starts. Agent code must not be able to read it at runtime. *Exercised:* [WISMO enforces this with a test](https://github.com/annagibaeva/wismo-returns-reliability-agent/blob/9e0fdb7fe703c2d7867a97c53d015547959ff838/tests/test_gold_isolation.py).

## 7. Refresh
After each rollout step, add confirmed production failures to the gold set. Every change bumps the dataset version recorded in the eval report.
