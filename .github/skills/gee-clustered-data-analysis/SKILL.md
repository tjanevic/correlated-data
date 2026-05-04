---
name: gee-clustered-data-analysis
description: Use when analyzing data with clustered or correlated observations (e.g., individuals nested in neighborhoods, schools, clinics) and you want population-average estimates with appropriately adjusted standard errors.
---

## Overview

When observations aren't independent, standard regression gives you wrong standard errors. Usually they're too small, which makes your p-values too optimistic. GEE fixes this by modeling the correlation structure between observations within the same cluster, then adjusting SEs accordingly.

GEE treats clustering as a nuisance. You're not trying to understand the variance between clusters. You just want valid inference for your exposure-outcome association at the population level.

## When to Use

- Individuals nested within groups (neighborhoods, clinics, schools, households)
- Repeated measures on the same person over time
- You care about population-average effects, not cluster-specific effects
- ICC is low-to-moderate and you don't need to model between-group variance explicitly
- Two levels of nesting (if you have three or more, consider MLM instead)

## Core Pattern

GEE works by specifying a working correlation matrix that describes how observations within a cluster relate to each other. The parameter estimates come out similar to standard regression. The difference is in the standard errors, which are corrected via sandwich estimation to account for the correlation you specified.

The four common working correlation structures:
- **Independent**: assumes no correlation within clusters (same as ignoring clustering)
- **Exchangeable**: all pairs within a cluster have the same correlation (most common for cross-sectional clustered data)
- **Autoregressive (AR-1)**: correlations decay with time/distance (useful for longitudinal data)
- **Unstructured**: estimates a unique correlation for every pair (flexible but requires more data)

Select the structure using QIC (Quasi-Likelihood Information Criterion). Lower QIC is better.

## Step-by-Step Process

**Step 1: Calculate the ICC**

Before anything else, quantify how correlated observations within clusters actually are. Run an empty random-intercept model (no predictors) in PROC MIXED and pull the variance components.

```
ICC = between-group variance / (between-group variance + within-group variance)
```

An ICC of 0.04 means 4% of variance in the outcome is due to between-cluster differences. Low, but still worth correcting.

**Step 2: Run standard regression (ignoring clustering)**

Run your model as if the data were independent. Record betas and SEs. This is your baseline comparison.

**Step 3: Run GEE**

Specify the cluster variable (e.g., zip code, school ID) and your working correlation structure. In SAS, use PROC GENMOD with the REPEATED statement. The default in SAS is unstructured.

**Step 4: Compare results**

The betas should be nearly identical between standard regression and GEE. What changes is the SEs. If clustering is real, GEE SEs will be larger than the naive SEs from standard regression.

**Step 5: Interpret**

GEE gives population-average estimates. The effect of income on BMI is the average effect across all neighborhoods, not the effect within any particular neighborhood.

## Judgment and Heuristics

If the ICC is very small (near zero), clustering barely matters and your standard regression results are probably fine. If the ICC is large, the clustering is doing real work and GEE (or MLM) is not optional.

When naive SEs are smaller than GEE SEs, you were getting anti-conservative inference from the uncorrected model. Some previously significant results will lose significance. This is correct behavior, not a problem.

Choice of working correlation matrix matters less than you'd think. GEE is robust to misspecification of the correlation structure, as long as you have enough clusters. With very few clusters, sandwich estimation becomes unstable.

Data should be in long format: one row per individual, with a cluster ID variable linking individuals to their group.

## Common Mistakes

**Ignoring clustering entirely.** SEs end up too small. You reject null hypotheses you shouldn't. This is the most consequential error.

**Confusing GEE with MLM.** GEE gives population-average effects. MLM gives cluster-specific effects. They answer different questions. For a neighborhood health survey, GEE tells you the average income-BMI relationship across NYC. MLM tells you the relationship within a specific neighborhood.

**Running GEE when you actually care about the cluster-level variance.** If your research question is about between-neighborhood differences, GEE can't answer it. Use MLM.

**Too few clusters.** GEE needs enough clusters for the sandwich estimator to work. Rule of thumb: at least 30-40 clusters. With 141 zip codes and 30 people each, you're in good shape.

**Forgetting to check the data are in long format.** GEE needs one row per individual with a group ID. Wide format won't work correctly.

## Quick Reference

| Feature | Standard Regression | GEE | MLM |
|---|---|---|---|
| Handles clustering | No | Yes (adjusts SEs) | Yes (models variance) |
| Beta interpretation | Individual | Population-average | Cluster-specific |
| ICC matters | N/A | Low-moderate best | Any, higher = more gain |
| >2 levels of nesting | No | No | Yes |
| Variance partitioning | No | No | Yes |
| SEs when clustering ignored | Too small | Corrected | Corrected |
| Use for | Independent data | Clustered, pop-average Q | Clustered, group effects Q |