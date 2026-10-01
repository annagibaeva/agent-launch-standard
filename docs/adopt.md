# Adopt the standard: gate sheet

Copy this file for each agent. Fill it in with the business owner at Design; update it at every stage. Criteria letters match [stages.md](stages.md). Status is **open**, **directional** or **closed**.

**Agent:** ______ **Risk tier (1 / 2 / 3):** ______ **Launch owner:** ______ **Business owner:** ______

## Design
- Design a · Severity classes and budget for each: ______
- Design b · Win condition (AND-clauses), thresholds chosen from [metrics.md](metrics.md) and the reason for each: ______
- Design d · Eval mix, and the score a refuse-everything agent would get: ______
- Design f · Human baseline (cost per contact · resolution time · CSAT · repeat contact): ______
- Design g · Acceptance criteria agreed in writing (link): ______
- Status: ______ Signed: ______ Date: ______

## Build
- Build a · Gate spec: what it can block, what it can never do (link): ______
- Build b · Policy file (link): ______
- Build c · Where actions are checked against the tool trace: ______
- Build d · Audit-trail schema (link): ______
- Build e · Personal data: where it is blocked and redacted: ______
- Status: ______ Signed: ______ Date: ______

## Eval
- Eval g · Gold set: source, size per slice, labelers, agreement ([gold-set.md](gold-set.md)): ______
- Eval a, f · Minimum n per clause, and whether each clause is passed or directional: ______
- Eval b · Detector validation result: ______
- Eval c · pass^k: k and temperature: ______
- Eval d · Held-out gap: ______
- Eval e · Internal score 100%? External test used and result: ______
- Eval report (link, [schema](eval-report-schema.md)): ______
- Status: ______ Signed: ______ Date: ______

## Launch
- Launch a, b · Pinned baseline and CI gate config (links): ______
- Launch c · State-graded acceptance test (link): ______
- Launch e · Integration-check log ([launch-and-operate.md](launch-and-operate.md)): ______
- Launch f · Sandbox run on the target system (link): ______
- Launch g · Rollout steps and exit criteria: ______
- Launch h · Rollback trigger per headline metric: ______
- Launch h · Kill switch: who can pull it; test date: ______
- Launch i · Monitoring dashboard (link): ______
- Status: ______ Signed: ______ Date: ______

## Expand
- New segment: ______
- Expand a · Segment eval report (link): ______
- Expand b · Original segment reproduces (link): ______
- Expand c · Budgets re-derived: ______
- Expand e · Production failures added to the gold set (dataset version): ______
- Status: ______ Signed: ______ Date: ______
