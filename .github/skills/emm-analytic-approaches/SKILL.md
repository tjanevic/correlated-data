---
name: emm-analytic-approaches
description: Use when choosing between stratified analysis with two reference groups and joint effects analysis with one reference group to assess effect measure modification.
---

## Overview

Once you have confirmed your research question involves effect modification, you have two analytic paths. Stratification with two reference groups and joint effects with one reference group answer related but slightly different versions of the question. Picking the right one depends on what you want to communicate.

Neither approach is universally better. The choice is about clarity for your specific question.

## When to Use

Use when you have a binary or categorical effect modifier Z and a binary exposure X, and you want to report how the X-Y association differs across strata of Z. You need a prior research question before running either approach.

## Core Pattern

**Stratification with two reference groups:** You split the data by Z. In each stratum, the unexposed group within that stratum is the reference. You get two stratum-specific effect estimates (e.g., RR among Z=1 and RR among Z=0). You compare those two estimates to each other to characterize the modification.

**Joint effects with one reference group:** You create a single combined variable that crosses X and Z, giving four cells (unexposed/Z=0, exposed/Z=0, unexposed/Z=1, exposed/Z=1). One cell, usually unexposed/Z=0, is the single reference for all comparisons. You can then evaluate additive and multiplicative interaction directly from the table.

What's interesting is that joint effects also lets you compare the combined exposure to what you would expect if X and Z acted independently. That expected joint effect is calculated from the independent effects, and the deviation from it is your interaction estimate. But the calculation depends on which scale you're working on.

## Step-by-Step Process

**For stratification:**

1. Define your strata of Z before analysis.
2. Within each stratum, compute the measure of association (RR, OR, RD) using the stratum-internal unexposed group as reference.
3. Report stratum-specific estimates side by side.
4. Assess whether estimates differ meaningfully across strata. This difference is your evidence of EMM.
5. Do not pool the estimates if EMM is present. The whole point is that they differ.

**For joint effects:**

1. Create a four-category combined variable crossing X (0/1) and Z (0/1).
2. Assign the unexposed/Z=0 cell as your single reference group.
3. Compute measures for each of the other three cells relative to that reference.
4. Calculate the expected joint effect assuming no interaction. The math depends on your scale:
   - On the additive scale (using risk differences), add the independent RDs: expected joint RD = RD_X + RD_Z.
   - On the multiplicative scale (using risk ratios), apply: expected joint RR = RR_X + RR_Z - 1, where the "-1" corrects for counting the baseline risk twice.
5. Compare observed joint effect to expected. Deviation is your interaction estimate.
6. Report the relative excess risk due to interaction (RERI) for additive scale, or ratio of joint effect to product of independent effects for multiplicative.

## Judgment & Heuristics

Stratification is cleaner to explain to a clinical or policy audience. "The effect of X is stronger in people with Z" reads directly from stratum-specific estimates.

Joint effects is more useful when you want to formally test departure from additivity or multiplicativity, or when you want to quantify the proportion of disease attributable to the interaction itself.

Additive interaction is usually more relevant for public health. If two exposures together cause more cases than you would expect from their independent effects, that matters for prevention regardless of whether multiplicative interaction is present.

You can have additive interaction without multiplicative interaction and vice versa. Always specify which scale you are working on. Many analyses report only one and miss the other.

Do not run both approaches and then report whichever makes your findings look more interesting. Pick one based on your question before the analysis.

## Common Mistakes

- Using a single reference group when you meant to do stratified analysis. The reference group choice changes the interpretation of every estimate in the table.
- Forgetting to specify the scale (additive vs multiplicative) when reporting interaction. "There was interaction" is incomplete.
- Running joint effects analysis and then treating the three non-reference cells as if they are stratum-specific estimates. They are not. They are all relative to the same baseline.
- Concluding no EMM because stratum-specific estimates are similar on the multiplicative scale, without checking the additive scale.
- Collapsing strata after finding no statistical significance for interaction. Absence of evidence is not evidence of absence, especially with small samples.
- Confusing the expected joint effect formula across scales. The additive formula (sum the RDs) is not interchangeable with the multiplicative formula (RR_X + RR_Z - 1).

## Quick Reference

| Feature | Stratification (2 reference groups) | Joint Effects (1 reference group) |
|---|---|---|
| Reference group | One per stratum | Single shared baseline |
| Output | Stratum-specific effect estimates | Four-cell combined exposure effects |
| Best for | Communicating effect differences clearly | Testing and quantifying interaction |
| Additive interaction | Compare RDs across strata | Expected joint RD = RD_X + RD_Z; compare to observed |
| Multiplicative interaction | Compare RRs across strata | Expected joint RR = RR_X + RR_Z - 1; compare to observed |
| Requires prior hypothesis | Yes | Yes |