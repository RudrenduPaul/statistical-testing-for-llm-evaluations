# Statistical Testing for LLM Evaluations

*Design experiments that catch the improvements worth shipping*

Companion notebooks for the O'Reilly live course **Statistical Testing for LLM Evaluations**, taught by **Rudrendu Paul**.

Your LLM eval says the new version is better. Should you trust it? Most LLM evaluations are underpowered, run the wrong statistical test, or measure a metric that does not survive production. These four notebooks give you the statistical toolkit to catch those failures before you ship. Every notebook runs on synthetic data, so `pip install` is the only setup required.

---

## Online vs. offline evaluation

Online evaluation, A/B testing on live traffic, carries a statistical playbook: sequential testing, online controlled experiments, established practice. Offline evaluation, the fixed-dataset comparison you run before anything ships, usually does not. This course closes that gap.

![Online vs. offline LLM evaluation](assets/online-vs-offline-evaluation-diagram.png)

Every notebook in this repo runs an offline comparison: a fixed dataset, no live users, old vs. new prompt or model compared side by side. The statistical toolkit here (power analysis, hypothesis testing) is what online experimentation carries and offline evals typically skip.

---

## Notebooks

| # | Notebook | What it shows | Open in Colab |
|---|----------|---------------|---------------|
| 01 | [Power analysis for LLM evals](notebooks/01-power-analysis-llm-evals.ipynb) | Why 50 examples cannot see a 5-point gain. Detecting a 5-point faithfulness gain on a noisy 0-100 judge score needs 847 examples per arm, not 50. | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RudrenduPaul/statistical-testing-for-llm-evaluations/blob/main/notebooks/01-power-analysis-llm-evals.ipynb) |
| 02 | [Hypothesis testing for LLM metrics](notebooks/02-hypothesis-testing-llm-metrics.ipynb) | The same 200 paired test cases where a paired t-test says "no difference" (p=0.10) and a Wilcoxon signed-rank test finds the improvement (p=0.0078), and why. | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RudrenduPaul/statistical-testing-for-llm-evaluations/blob/main/notebooks/02-hypothesis-testing-llm-metrics.ipynb) |
| 03 | [RAG evaluation case study](notebooks/03-rag-evaluation-case-study.ipynb) | A 14-point retrieval-recall drop (0.82 to 0.68) behind a 6-point end-to-end drop (0.80 to 0.74), and the component table that shows where it came from. | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RudrenduPaul/statistical-testing-for-llm-evaluations/blob/main/notebooks/03-rag-evaluation-case-study.ipynb) |
| 04 | [Agent evaluation mini-case](notebooks/04-agent-evaluation-mini-case.ipynb) *(bonus)* | Bonus. In a simulation, the agent that leads on final-answer quality falls behind under production constraints (84.5% to 70.5% task success) while the agent with better process metrics holds up (81.5% to 79.5%). | [![Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/RudrenduPaul/statistical-testing-for-llm-evaluations/blob/main/notebooks/04-agent-evaluation-mini-case.ipynb) |

> **Notebook 04 is a bonus.** The live course covers power analysis, hypothesis testing, and the RAG case in 60 minutes. The agent notebook is an optional extra to explore on your own.

> **Open in Colab:** click any badge to launch the notebook in Google Colab (no local setup). You can also download the `.ipynb` and upload it to Colab, or run it locally.

All notebooks ship with their outputs saved, so you can read the charts and tables without running a single cell.

---

## Run locally

```bash
git clone https://github.com/RudrenduPaul/statistical-testing-for-llm-evaluations.git
cd statistical-testing-for-llm-evaluations
python -m venv .venv && source .venv/bin/activate   # optional
pip install -r requirements.txt
jupyter lab
```

Python 3.10+ is recommended. No API keys are required. All data is synthetic and generated inside each notebook.

---

## 5-Question Diagnostic Framework

Before you act on any eval result, run it through five questions. The one-page version is in [`5-question-diagnostic-framework.md`](5-question-diagnostic-framework.md).

![The 5-Question Diagnostic Framework](assets/five-question-diagnostic-diagram.png)

1. Does the metric measure what matters?
2. Was the experiment randomized?
3. Was the sample large enough?
4. Is the LLM-as-judge unbiased?
5. Will the offline result hold once it meets live users?

---

## FAQ

**How many examples do you need to detect a change in an LLM eval?**
It depends on the effect size and the metric's noise. Detecting a 5-point faithfulness gain on a noisy 0-100 judge score needs 847 examples per arm in the notebook's simulation (score SD about 37), not the 50 most teams run.

**Which statistical test catches an LLM improvement a t-test misses?**
On paired eval data, a Wilcoxon signed-rank test can catch an improvement a t-test reports as no difference (p=0.10 for the t-test vs. p=0.0078 for Wilcoxon in notebook 02).

**Can a small end-to-end RAG dip hide a retrieval regression?**
Yes. Notebook 03 works through a 14-point retrieval-recall collapse (0.82 to 0.68) hidden behind a 6-point end-to-end dip (0.80 to 0.74), and the component-level evaluation design that surfaces it.

**Does an agent that leads a benchmark hold up in production?**
Not always. In the simulated bonus notebook 04, Agent A leads the benchmark (0.845 task success) then drops under production constraints (0.705), while Agent B holds (0.795) and ends ahead.

---

## Further reading (all O'Reilly)

- *Practical Statistics for Data Scientists*, Peter Bruce, Andrew Bruce, and Peter Gedeck (O'Reilly)
- *Evals for AI Engineers*, Shreya Shankar and Hamel Husain (O'Reilly, 2026)
- *AI Engineering: Building Applications with Foundation Models*, Chip Huyen (O'Reilly, 2025)

---

## Connect

- LinkedIn: [linkedin.com/in/rudrendupaul](https://www.linkedin.com/in/rudrendupaul)
- GitHub: [github.com/RudrenduPaul](https://github.com/RudrenduPaul)
- ORCID: [0009-0008-0141-4690](https://orcid.org/0009-0008-0141-4690)
- Medium: [medium.com/@rudrendupaul](https://medium.com/@rudrendupaul)

Licensed under the MIT License. See [LICENSE](LICENSE).

