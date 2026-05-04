---
name: poisson-regression-rates
description: Use when modeling count outcomes (deaths, disease cases, events) with varying person-time across observations, especially in cohort studies with grouped data.
---

## Overview

Poisson regression models rates, not raw counts. That distinction matters more than it sounds.

Say you're studying lung cancer deaths in two groups of workers. Group A had 10 deaths. Group B had 10 deaths. Same number, so same risk? Not if Group A was followed for 1,000 person-years and Group B was followed for 10,000. Group A's rate is ten times higher. Same count, completely different story.

Person-time is the denominator of a rate. Person-years, person-months, whatever unit fits your study. When each row in your dataset represents a different group of people followed for different lengths of time, you can't just compare counts. You have to compare counts relative to how long each group was under observation.

That's what the offset does. It tells the model: don't just predict the count, predict the count given this much observation time. Without it, groups with longer follow-up will always look like they have more events, because they do. But that's not the same as having a higher rate.

The output is an Incidence Rate Ratio (IRR). The coefficient from the model is on the log scale; exponentiate it to get the IRR.

## When to Use

- Count outcomes (deaths, hospitalizations, cases) with varying follow-up time per row
- Grouped cohort data where each row represents a pattern of covariates with a summed count and person-time
- When you want IRRs as your effect measure
- When the outcome is rare enough that Poisson is a reasonable approximation (also used for common outcomes with robust variance)

## Core Pattern

The Poisson model:

```
log(rate) = log(count/person-time) = b0 + b1*X1 + b2*X2 + ...
```

Rearranged:

```
log(count) = log(person-time) + b0 + b1*X1 + ...
```

The `log(person-time)` term is the offset. It has a coefficient forced to 1, not estimated. Everything else is estimated normally.

Exponentiate any beta to get an IRR:
```
IRR = exp(beta)
```

An IRR of 2.08 means the rate of the event in the exposed group is 2.08 times the rate in the reference group.

## Step-by-Step Process

**Step 1: Check your data structure**

For grouped data: each row is a covariate pattern. The outcome column is the total event count for that pattern. You also need a person-years (or person-time) column.

For individual data: each row is one person. Outcome is 0/1 (event occurred). Person-time is their follow-up duration.

**Step 2: Specify the offset**

In SAS PROC GENMOD, use the OFFSET= option or the OFFSET statement. The offset is log(person-time), not person-time itself. Some software takes person-time and logs it automatically; check your documentation.

```sas
proc genmod data=yourdata;
  class arsenic agegrp / param=ref;
  model rescadth = arsenic / dist=poisson link=log offset=logpyrs;
run;
```

**Step 3: Interpret the IRR**

Exponentiate the beta. The reference category gets IRR = 1.00. All other categories are compared to it.

For ≥15 years arsenic exposure vs. ≤1 year, beta = 1.41, so:
```
IRR = exp(1.41) = 4.08
```
Those exposed 15+ years have 4 times the rate of respiratory cancer deaths.

**Step 4: Assess confounding**

Run the crude model first. Then add potential confounders one at a time (or in theoretically motivated blocks). If a parameter estimate changes appreciably (rule of thumb: >10%), confounding is present.

In the Montana smelter data, adding age group changed the arsenic IRRs substantially. The IRR for ≥15 years dropped from 4.08 to 2.71 after adjusting for age. Age confounds the relationship because both arsenic duration and cancer risk increase with age.

**Step 5: Test effect modification**

Add an interaction term between your exposure and the potential modifier. Assess statistical significance of the interaction term. A non-significant interaction p-value means no strong evidence for multiplicative effect modification.

## Judgment and Heuristics

Confounding check: compare crude to adjusted estimates. A 10% change in any parameter estimate is a common threshold for calling something a confounder. This is a guideline, not a law.

Effect modification: a significant interaction term means the exposure effect differs across strata of the modifier. Run stratified models and report stratum-specific IRRs when effect modification is present.

Poisson assumes the mean equals the variance. Real count data often has overdispersion (variance > mean). If that's the case, use negative binomial regression or apply a scale parameter correction. Signs of overdispersion: deviance/df ratio well above 1.

With very large grouped datasets, the Poisson model may fit badly due to sparse cells. Check that no covariate patterns have zero person-time.

## Common Mistakes

**Forgetting the offset.** Without it, you're modeling counts instead of rates. Groups with more person-time will appear to have higher rates just because they were observed longer. This is the whole game with Poisson regression.

**Using person-time instead of log(person-time) as the offset.** The model works on the log scale. You need log(person-years), not raw person-years.

**Interpreting the beta instead of the IRR.** The beta is the log IRR. Always exponentiate before interpreting.

**Ignoring overdispersion.** If your count data has more variability than the Poisson distribution assumes, your standard errors will be too small. Check the deviance/df ratio.

**Treating an IRR of 1.0 in the reference category as a result.** It's a constraint, not a finding. The reference category is what everything else is compared to.

## Quick Reference

| Term | Definition | Formula |
|---|---|---|
| Offset | Log of person-time; accounts for varying observation | log(person-years) |
| IRR | Incidence Rate Ratio; main effect measure | exp(beta) |
| Crude model | No covariates beyond exposure | Baseline |
| Adjusted model | Adds potential confounders | Compare betas to crude |
| Interaction test | Effect modification on multiplicative scale | Check p-value of product term |
| Confounding threshold | Rule of thumb for meaningful change | >10% change in beta |

| Arsenic duration | Beta | IRR | Interpretation |
|---|---|---|---|
| ≤1 yr (ref) | 0 | 1.00 | Reference |
| 1-<5 yrs | 0.80 | 2.23 | 2.2x rate vs. reference |
| 5-<15 yrs | 0.60 | 1.81 | 1.8x rate vs. reference |
| ≥15 yrs | 1.00 | 2.71 | 2.7x rate vs. reference |