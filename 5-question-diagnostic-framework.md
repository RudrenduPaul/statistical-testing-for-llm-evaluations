# 5-Question Diagnostic Framework

![The 5-Question Diagnostic Framework](assets/five-question-diagnostic-diagram.png)

**Before you ship, ask these five questions.**

Use this checklist before promoting any LLM change to production. Each question targets a specific problem that offline evals routinely miss. If any red flag fires, stop and fix the underlying problem before shipping.

---

## Q1: Does the metric measure what matters?

**What to check:**
- Is the eval metric a direct measure of user satisfaction, or a proxy that correlates with it only loosely?
- Has the metric been validated against production outcomes? (For example: does a higher ROUGE score predict that users prefer the output?)
- Could a model change improve the metric without improving the user experience?

**Red flag:** No one has cross-validated the metric against human preference data, user ratings, or downstream task completion rates. You are optimizing a number with no confirmed link to user value.

**If the check fails:** Run a human evaluation on a stratified sample (minimum 100 examples) to calibrate the metric against direct human judgment before using it as a gating criterion.

---

## Q2: Was the experiment randomized?

**What to check:**
- Does a valid control group exist, with no treatment applied?
- Are test cases randomly assigned to conditions, not selected by convenience or recency?
- Could selection bias explain the result? (Curated "hard" examples, cherry-picked failure cases, or dev-team-authored prompts all introduce it.)

**Red flag:** The test set came from examples the team knew the model struggled with, or from examples where it succeeded. Neither is a random sample.

**If the check fails:** Reconstruct the eval set by random sampling from production logs. If no production logs exist, draw from a representative distribution of expected query types and document the sampling methodology explicitly.

---

## Q3: Was the sample large enough?

**What to check:**
- How many test cases were used? Fewer than 200 is almost always insufficient for detecting meaningful improvements.
- What effect size can this sample detect at 80% statistical power? In notebook 01's simulation (score SD about 37), 50 examples yield 10.4% power for a 5-point gain and 847 per arm reach 80%. Your number depends on your smallest worthwhile effect and your metric's noise, so compute it before collecting data.
- Did someone run a power calculation, or was the sample size chosen arbitrarily?

**Red flag:** The eval ran on fewer than 100 examples and the reported improvement is smaller than 5 percentage points. At that scale, the result is consistent with noise.

**If the check fails:** Do not ship based on the current result. Run a prospective power analysis, collect the required sample, and re-evaluate. Document the power calculation alongside the results.

---

## Q4: Is the LLM-as-judge unbiased?

**What to check:**
- Has position bias been ruled out? Swap the A/B order and confirm the winner does not change.
- Has verbosity bias been controlled for? LLM judges systematically prefer longer outputs regardless of quality, so scores should be checked against length-normalized outputs.
- Has self-preference bias been ruled out? A model judging outputs from its own model family will favor those outputs over outputs from a different architecture, so the judge should be calibrated against a cross-family baseline.

**Red flag:** The same model family serves as both the system under test and the judge, and position bias testing has not been run. Any result from this setup is unreliable.

**If the check fails:** Add a position-swap test (run every comparison twice with order reversed and take the majority vote), strip outputs to similar length before presenting to the judge, or switch to a judge from a different model family. Calibrate the judge against human labels on at least 50 examples.

---

## Q5: Will the offline result hold once it meets live users?

**What to check:**
- Does the eval dataset reflect production queries, including edge cases, multilingual inputs, adversarial phrasings, and length distribution?
- Has the system been tested under production constraints: latency budgets, context window limits, concurrent request load, and retrieval latency for RAG pipelines?
- Is there evidence of eval gaming? The metric improves, but qualitative review of outputs reveals degradation in dimensions the metric does not capture.

**Red flag:** The eval set was authored by the development team without sampling from production logs, and the system has never run under realistic latency or load conditions.

**If the check fails:** Run a shadow deployment or a staged rollout with production traffic before full promotion. Establish a monitoring baseline on the production metric (user ratings, task completion, escalation rate) and set an alert threshold before you ship.

---

## Quick reference

| Question | Core risk | Minimum bar |
|---|---|---|
| Metric measures what matters? | Proxy optimization with no user benefit | Validated against human judgment |
| Proper randomization? | Selection bias inflates results | Random sample from prod logs or documented distribution |
| Sample large enough? | Noise mistaken for signal | Size it from your smallest worthwhile effect and metric noise (847 per arm in notebook 01) |
| Judge is unbiased? | Systematic preference for length or model family | Position swap + cross-family judge |
| Offline result survives production? | Eval gaming; distribution shift | Shadow deployment or staged rollout |

If the answer to all five questions is Yes, all five pass: ship. If any answer is No: hold, gather more evidence, or run a causal check.

---

*From the O'Reilly Live Course: **"Statistical Testing for LLM Evaluations: Design experiments that catch the improvements worth shipping"** by Rudrendu Paul.*

---

**Sources:**

- Position bias and verbosity bias in LLM-as-judge: Zheng et al. (2023), "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena," NeurIPS 2023 ([arxiv.org/abs/2306.05685](https://arxiv.org/abs/2306.05685)).
- Self-preference bias: Panickssery et al. (2024), "LLM Evaluators Recognize and Favor Their Own Generations" ([arxiv.org/abs/2404.13076](https://arxiv.org/abs/2404.13076)).
