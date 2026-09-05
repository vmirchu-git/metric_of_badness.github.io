<p align="center">
  <img src="assets/badness_score_diagram.svg" alt="Badness Score pipeline diagram" width="100%">
</p>

<p align="right"><a href="README.ru.md">Русская версия →</a></p>

# Badness Score: a Universal Metric for Intent Quality Monitoring

![Python](https://img.shields.io/badge/Python-pandas-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)
![Airflow](https://img.shields.io/badge/Airflow-ETL-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)
![Power BI](https://img.shields.io/badge/Power%20BI-dashboards-14131a?style=flat-square&labelColor=14131a&color=7a1f2b)
![status](https://img.shields.io/badge/status-adopted-14131a?style=flat-square&labelColor=14131a&color=c9a227)

> **Note on source code.** This repository documents methodology, design decisions, and results only. The implementation is proprietary to the employer where this project was built and is not published here.

A single composite metric that turns a scattered, fragmented view of intent quality into one ranked, role-adjustable priority list — so any team could immediately see which intents needed attention most, without running a separate analysis every time.

## At a glance

| | |
|---|---|
| **Problem-intent detection time** | reduced by 80%+ |
| **Detection accuracy** | 90% of problem intents caught on the first pass, confirmed in practice |
| **Analyst time saved** | up to 20 hours per month |
| **Scale** | applied across all chatbot channels and intents; used by every relevant role, not a single team |
| **Status** | adopted by the team currently building chatbot automation services |

## Problem

The team managed a large pool of chatbot intents with no unified way to spot which ones were degrading. Evaluation was fragmented across separate, disconnected metrics, which made prioritization across teams genuinely hard — a low-performing intent could go unnoticed simply because no single metric was bad enough on its own to trigger attention, while the combination of several mediocre metrics was quietly causing real damage: more automation errors, higher operator workload, and SLA delays.

## Approach

**The core idea.** Badness Score isn't tied to a fixed set of metrics — it's a weighted combination where any metric can be added:

```
Badness_Score = w1·(1 − accuracy) + w2·(1 − operator_time) + ...
```

Each stakeholder can adjust the weights to match what they actually care about — an operations lead might weight operator handling time heavily, while a product manager might prioritize CSAT — without anyone needing to rebuild the underlying pipeline or recompute anything by hand.

**Data pipeline.** An Airflow-orchestrated ETL job collects the full set of input metrics (accuracy, automation rate, CSAT, drop-off rate, operator handling time, and others) for every intent, on a recurring schedule.

**Preprocessing.** Outliers are flagged via percentile thresholds, missing values are handled explicitly rather than silently dropped, and metrics are normalized (Z-score and MinMax were both tested) so that combining metrics on very different scales doesn't quietly bias the result toward whichever one happens to have the largest raw numbers.

**Visualization.** Power BI dashboards surface the top-10 worst-performing intents, with the ranking updating automatically as the underlying weights change per viewer role.

**Validation.** The metric's ability to flag genuinely problematic intents was checked against manual expert assessments, using recall for problem-intent identification (target threshold above 0.8) as the validation criterion.

**LLM assistance.** An LLM was used to speed up classification of newly created intents and to generate test examples during development.

## Results

- Reduced the time to detect problem intents by more than 80%.
- Caught 90% of genuinely problem intents on the first pass — confirmed against expert review, not just a design target.
- Different roles could see their own top-10 priority list instantly, based on the metrics that mattered most to them, instead of waiting on a custom analysis.
- Surfaced high-volume intents that hadn't previously been flagged as problematic under the old, fragmented evaluation approach.

## Business impact

- Reduced SLA violations by catching quality degradation earlier.
- Lowered operator workload by addressing root causes sooner.
- Saved up to 20 hours of analyst work per month that had previously gone into fragmented, manual evaluation.
- Contributed to a measurable increase in overall chatbot accuracy.

## Tech stack

Python · pandas · NumPy · scikit-learn · SQL · Airflow · Power BI · Jupyter Notebook

---

<sub>Individual project completed as part of a Data / ML Analytics role. Described here for portfolio purposes; production code is not publicly available.</sub>
