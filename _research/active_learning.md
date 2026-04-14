---
layout: page
title: mcmc guided active learning
description:
img: assets/img/project_covers/active_learning.png
importance: 2
category: tools
related_publications: true
---

A central challenge in Bayesian inverse modelling of mechanical systems is cost: evaluating the forward model (e.g. a FEM simulation) is expensive, and MCMC requires many such evaluations.
**Surrogate models** replace the expensive simulator with a cheap approximation, but their accuracy hinges critically on _where_ training data is collected.

## The problem with a priori training

Most existing works collect training data before inference begins — sampling from the prior, a Latin hypercube, or a grid.
This seems reasonable, but it ignores where the posterior actually lives.
In {% cite riccius2025activelearningmcmc %} we show that this leads to large errors in the posterior estimation, even for moderate dimensionalities (5–10 parameters), because the curse of dimensionality makes it increasingly unlikely that a priori data falls in regions of high posterior density.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/active_learning/online_vs_offline.png" title="Online vs offline surrogate training" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Approximation error of a GP surrogate trained with different data collection strategies, across latent space dimensionalities d=2 to d=5. Active learning (AL) consistently outperforms a priori strategies (Grid, LHS, Prior), with the gap widening as dimensionality increases.
</div>

## MCMC-guided active learning

We propose a simple active learning strategy: collect training data by **following the MCMC chain itself**.
As the sampler explores the posterior, each proposed point is evaluated with the expensive FEM model only if the GP surrogate's uncertainty exceeds a threshold — otherwise the surrogate prediction is used.
This focuses model evaluations precisely where the posterior has support, without any prior knowledge of its location.

The method is training-algorithm agnostic.
We compare two samplers — Random Walk Metropolis (RWM) and the Metropolis-adjusted Langevin algorithm (MALA) — and find they produce surrogate models of comparable quality.
MALA's higher sampling efficiency yields slightly fewer FEM evaluations for the same surrogate accuracy under tight uncertainty budgets, but RWM is more robust under relaxed conditions.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/active_learning/sampler_influence.png" title="MCMC sampler influence on surrogate quality" class="img-capped rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Influence of the MCMC sampler (RWM vs MALA) on surrogate model quality and inference accuracy. Both algorithms produce comparable surrogate models; the choice of sampler has only a minor effect on overall performance.
</div>

## The real bottleneck is the forward model

A key finding of the study is that **the forward model, not the MCMC algorithm, is the bottleneck**.
Related works commonly invest significant effort in integrating advanced gradient-based MCMC algorithms (HMC, NUTS), but our analysis shows that the training data requirements for a surrogate model in high-dimensional settings make the MCMC-with-surrogate approach impractical long before the benefits of these algorithms can be realised.

For a simulator taking just 10 minutes per run, constructing a surrogate for a 15-dimensional latent space would require roughly 70 days of wall-clock time.
Any gains from a more efficient sampler are irrelevant at this scale.

This shifts the research priority: instead of tuning MCMC algorithms, future work should focus on reducing the surrogate's data requirements through multi-fidelity schemes or nonlinear dimensionality reduction.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/active_learning/graphical_abstract.png" title="Graphical abstract" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Graphical abstract: the active learning loop couples the MCMC sampler with the GP surrogate to adaptively collect training data from regions of high posterior density.
</div>
