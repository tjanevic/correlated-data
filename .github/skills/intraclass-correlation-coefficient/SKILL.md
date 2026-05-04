---
name: intraclass-correlation-coefficient
description: Use when quantifying how similar observations within the same cluster are, before deciding how to handle clustered data in regression.
---

## Overview

The ICC measures the proportion of total variance in an outcome that is attributable to differences between clusters rather than differences between individuals within clusters. It tells you how much "information" is actually in your clustered dataset.

If individuals within a cluster are nearly identical, adding more individuals per cluster stops giving you new information. That's what a high ICC looks like.

## When to Use

- Before any GEE or MLM analysis, to understand the degree of clustering
- When deciding whether to bother correcting for clustering at all
- When explaining why standard regression underestimates standard errors
- When comparing how clustered different outcomes are within the same design

## Core Pattern

The ICC formula:

```
ICC = variance_between / (variance_between + variance_within)
```

Where:
- `variance_between` = between-cluster variance (the Intercept estimate in PROC MIXED)
- `variance_within` = within-cluster variance (the Residual estimate in PROC MIXED)

To get these components, run an empty random-intercept model: outcome only, no predictors, with cluster as the random effect. In SAS, this is PROC MIXED with a RANDOM intercept / SUBJECT=cluster_id statement.

The output table "Covariance Parameter Estimates" gives you both numbers directly.

## Step-by-Step Process

**Step 1: Run the empty model**

No covariates. Just the outcome and the cluster structure. The goal is to decompose total variance into between-cluster and within-cluster components without any covariates eating up that variance.

**Step 2: Pull the variance components**

From "Covariance Parameter Estimates":
- Intercept row = between-group variance
- Residual row = within-group variance

**Step 3: Calculate ICC**

Plug into the formula. For example, if between = 0.97 and within = 23.69:

```
ICC = 0.97 / (0.97 + 23.69) = 0.97 / 24.66 = 0.039
```

**Step 4: Interpret**

3.9% of the variance in BMI is due to between-neighborhood differences. The correlation between any two residents in the same neighborhood is 0.039.

## Judgment and Heuristics

ICC = 0 means individuals within clusters are no more similar than individuals from different clusters. Clustering doesn't matter. Standard regression is fine.

ICC = 1 means all variation is between clusters, none within. Everyone in a cluster is identical. Adding more people per cluster adds zero information.

In practice, most real-world ICCs in neighborhood health research are small (0.01 to 0.10). Even small ICCs matter when you have large clusters, because the effective sample size shrinks.

The effective sample size under clustering is approximately:
```
n_eff = n / (1 + (m-1) * ICC)
```
where m is the cluster size. With ICC = 0.039 and cluster size 30, n_eff is roughly 0.47 * n. You're losing more than half your effective sample size.

Higher ICC is an argument for MLM over GEE. If between-cluster variance is large enough to be interesting in its own right, you probably want to model it explicitly.

## Common Mistakes

**Running the ICC on a model with covariates.** Covariates absorb variance. The ICC from a model with predictors reflects residual clustering after adjusting, not total clustering. Run the empty model for a clean ICC.

**Assuming low ICC means clustering is ignorable.** Even a small ICC inflates type I error if clusters are large. Check the effective sample size calculation before deciding.

**Treating ICC as fixed.** The ICC is outcome-specific. An outcome heavily influenced by neighborhood factors (air quality, food access) will have a higher ICC than one driven by genetics. Don't assume the ICC from one outcome applies to another.

**Confusing within and between variance in the formula.** Between-cluster variance goes in the numerator. The full formula has both in the denominator. Flipping them gives you 1 - ICC, which is a different thing.

## Quick Reference

| ICC Value | Interpretation | Action |
|---|---|---|
| ~0 | No clustering effect | Standard regression probably fine |
| 0.01-0.05 | Low clustering | Correct SEs with GEE; small but real effect |
| 0.05-0.20 | Moderate clustering | GEE or MLM; check effective sample size |
| >0.20 | High clustering | MLM preferred; between-cluster variance is substantively interesting |
| 1.0 | All variance between clusters | Each cluster is one observation effectively |

| Term | SAS location | Formula role |
|---|---|---|
| Between-cluster variance | Intercept row, Covariance Parameter Estimates | Numerator |
| Within-cluster variance | Residual row, Covariance Parameter Estimates | Denominator (partial) |
| Total variance | Sum of both | Denominator (full) |