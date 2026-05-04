---
name: effect-measure-modification-concepts
description: Use when distinguishing effect modification from confounding or mediation, or when deciding whether a third variable changes an exposure-disease association across strata.
---

## Overview

Effect measure modification (EMM), confounding, and mediation all involve a third variable (Z) and an exposure-outcome pair (X → Y). They are not the same thing. Conflating them is one of the most common errors in epidemiologic analysis.

EMM is present when the strength of the X-Y association differs across levels of Z. It is not a bias. It is a real feature of how the exposure works in the world. You want to find it. Confounding, by contrast, distorts the exposure-disease association and is a source of bias you want to remove. Mediation describes the causal pathway, not a modifier of the effect.

## When to Use

Use when you have a covariate Z and you're asking: does the effect of X on Y depend on the level of Z? If yes, you have effect modification. Use this framework before you start any stratified analysis or joint effects table.

Another use is when you want to test whether the effect of X and Z combined on Y is greater than the risk of either X or Z alone.

## Core Pattern

Three distinct roles a third variable can play:

**Effect modifier:** The effect of X on Y differs by levels of Z. Not a bias. You report it, you do not "control" for it. The stratum-specific estimates are the finding.

**Confounder:** Z distorts the observed X-Y association away from the truth. It is a source of bias. You adjust for it, you do not stratify and report each stratum as a separate finding.

**Mediator:** Z is the mechanism by which X causes Y. Adjusting for a mediator blocks the causal path and gives you the wrong answer. Identifying mediators is about understanding *how* the exposure works.

What's interesting here is that the same variable can technically be a confounder in one analysis and an effect modifier in another, depending on your research question and the causal structure you're assuming.

## Step-by-Step Process

1. **State your research question explicitly.** EMM analysis requires a hypothesis going in. "Does Z modify the effect of X on Y?" has to be written down before you look at the data. If you decide to look for EMM after seeing stratified results, you are data dredging.

2. **Decide on your scale before analysis.** Will you evaluate EMM on the multiplicative scale (do the risk ratios differ?) or the additive scale (do the risk differences differ?), or both? This matters because a variable can show interaction on one scale but not the other. Your research question determines which scale answers the question you actually want to answer.

3. **Classify Z before analysis.** Is Z a confounder, a mediator, or a potential effect modifier? Use your DAG or prior knowledge. Do not rely on statistical tests alone to classify it.

4. **Check whether adjustment is appropriate.** For effect modifiers, do not adjust. Report stratum-specific estimates. For confounders, adjust. For mediators, do not adjust if you want the total effect.

5. **Look at stratum-specific estimates.** If RR in Z=1 stratum differs meaningfully from RR in Z=0 stratum, EMM is present on the multiplicative scale. If the risk difference in Z=1 stratum differs from the risk difference in Z=0 stratum, EMM is present on the additive scale. These are separate findings.

6. **Apply judgment to magnitude.** Once you've found what you pre-specified looking for, ask: is this difference clinically or biologically meaningful? This judgment filters whether a pre-hypothesized finding is worth reporting. It does not rescue a finding you never predicted looking for.

7. **Choose your analytic approach.** Two options: stratification with two reference groups, or joint effects with one reference group. Pick based on your question before you run the analysis.

## Judgment & Heuristics

Effect modification tells you something about causal pies. If the effect of X differs across strata of Z, Z is likely part of the same sufficient cause as X for at least some outcomes. Z and X are causal partners.

Confounding is about exchangeability. EMM is about biology or context. They require different responses.

The pre-analysis hypothesis governs whether you look for EMM at all. Post-hoc clinical judgment then filters the magnitude of a pre-specified finding, not whether a difference you stumbled on constitutes a real finding. Magnitude judgment without a prior hypothesis is data dredging.

A difference on the multiplicative scale does not automatically mean a difference on the additive scale, and vice versa. Both matter for public health impact. Report what your hypothesis called for.

## Common Mistakes

- Confusing a variable that changes the point estimate when adjusted (confounder) with one that changes stratum-specific estimates (effect modifier). These look superficially similar but are conceptually opposite.
- Running stratified analysis without a prior hypothesis about EMM, then reporting the largest stratum as the finding.
- Not pre-specifying additive vs. multiplicative scale, then choosing the scale that shows interaction after you see the data.
- Adjusting for a mediator when you want the total causal effect of X on Y.
- Using post-hoc clinical judgment to rescue a finding you never predicted looking for.
- Skipping the research question step and going straight to the stratified table.

## Quick Reference

| Concept | What it means | What you do | Goal |
|---|---|---|---|
| Effect modification | Effect of X on Y differs by Z | Report stratum-specific estimates | Find it, describe it |
| Confounding | Z distorts the X-Y association | Adjust for Z | Remove the bias |
| Mediation | Z is the pathway from X to Y | Do not adjust (for total effect) | Understand the mechanism |
| Causal partners | X and Z co-appear in a sufficient cause | Identify via EMM analysis | Map causal structure |