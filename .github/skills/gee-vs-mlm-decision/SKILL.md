---
name: gee-vs-mlm-decision
description: Use when deciding whether to analyze clustered data with GEE or multilevel/mixed-effects models, and when explaining the tradeoffs to collaborators or reviewers.
---

## Overview

GEE and multilevel models (MLM, also called mixed-effects models or hierarchical models) both handle clustered data. They're solving related but different problems, and they answer different research questions.

Getting this choice wrong doesn't necessarily give you wrong estimates, but it might give you estimates that don't answer your actual question.

## When to Use

- Any clustered data analysis where the choice isn't predetermined by convention
- When a reviewer asks why you chose one over the other
- When you have a research question about both individual-level and group-level effects
- When ICC is high enough that the clustering structure is substantively interesting

## Core Pattern

**GEE asks:** What is the population-average effect of X on Y?

The answer ignores cluster-to-cluster variation as a substantive question. You're saying: across all the clusters in the population, on average, what does a one-unit change in X do to Y? The clustering is accounted for in the SEs, but you're not modeling between-cluster differences.

**MLM asks:** What is the effect of X on Y within clusters, and how much does that effect (or the baseline level of Y) vary across clusters?

The answer explicitly models both the fixed effects (like GEE) and the random effects (the cluster-level deviations). You can estimate cluster-specific predictions, partition variance between levels, and understand what drives between-cluster differences.

This is why their beta estimates can differ slightly. GEE gives a marginal (population-average) estimate. MLM gives a conditional (cluster-specific) estimate. For linear models the difference is small. For non-linear models (logistic, Poisson) the difference can be substantial.

## Step-by-Step Decision Process

**Question 1: What is your research question?**

If you want to say "people in higher-income neighborhoods have lower BMI than people in lower-income neighborhoods, on average", that's a population-average question. GEE is natural here.

If you want to say "controlling for individual income, what neighborhood-level factors explain why some neighborhoods have systematically higher BMI", that's a multilevel question. You need MLM.

**Question 2: How many levels of nesting do you have?**

Two levels (individuals in neighborhoods): either GEE or MLM works.

Three or more levels (students in classrooms in schools): GEE can't handle this well. Use MLM.

**Question 3: What is the ICC?**

Low ICC: clustering has modest impact. GEE is sufficient for SE correction.

High ICC: substantial between-cluster variation exists. MLM lets you model and explain it. GEE just adjusts around it.

**Question 4: What is your cluster sample size?**

MLM needs sufficient observations per cluster for stable estimation of random effects. Very small clusters (fewer than 5-10 per group) cause problems for MLM. GEE is more robust here.

With 30 individuals per neighborhood (as in the NYC CHS example), MLM is fine.

## Judgment and Heuristics

For most epidemiological studies where clustering is a design feature (surveying households within neighborhoods), GEE is the default unless you have a substantive between-cluster question.

If your collaborator says "I want to control for neighborhood" but they don't actually want neighborhood-level estimates, GEE is probably what they mean.

MLM results from the NYC CHS example: point estimates shifted slightly from GEE, SEs looked more like naive regression than GEE. This happens because MLM borrows strength across clusters and shrinks estimates toward the grand mean (partial pooling). It's not wrong, it's a feature.

Publications in social epidemiology often expect MLM when the clustering is theoretically meaningful (neighborhoods, hospitals). Publications in clinical epidemiology with clustering as a nuisance (multicenter trials) often use GEE or robust SEs.

When results differ between GEE and MLM in ways that change conclusions, report both and explain why they differ. It's usually informative about the data structure.

## Common Mistakes

**Using MLM just because it sounds more sophisticated.** MLM requires more assumptions (distribution of random effects, usually normal). It also requires more observations per cluster. GEE is often the right tool.

**Expecting identical estimates from GEE and MLM.** They estimate different quantities, especially for binary or count outcomes. Similar estimates in a linear model context doesn't mean they'd be similar for logistic regression.

**Ignoring small cluster sizes when using MLM.** With clusters of 5 or fewer, the random effect variance estimates are unstable. GEE handles sparse clusters better.

**Confusing random effects with fixed effects for covariates at the cluster level.** A neighborhood-level variable (poverty rate, park access) is a fixed effect in MLM, not a random effect. The random intercept captures residual between-cluster variation after accounting for those variables.

## Quick Reference

| Feature | GEE | MLM |
|---|---|---|
| Effect type | Population-average (marginal) | Cluster-specific (conditional) |
| Between-cluster variance | Treated as nuisance | Explicitly modeled |
| Research question | Average effect across population | Effects within/between clusters |
| Handles 3+ levels | No | Yes |
| Min cluster size | Flexible (needs enough clusters) | ~5-10 per cluster minimum |
| High ICC | Adjust SEs, but doesn't explain | Model and explain the variance |
| Variance partitioning | No | Yes |
| Software complexity | Lower | Higher |
| Default when clustering is a nuisance | Yes | No |
| Default when cluster effects are of interest | No | Yes |