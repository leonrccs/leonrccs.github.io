---
layout: page
title: pdmp sampling
description: 
img: assets/img/project_covers/zz.gif
importance: 1
category: tools
related_publications: true
---

I work on developing tools for **making Bayesian inference practical for computational mechanics**.
These problems arise on a number of real-life applications that are often too cumbersome or error-prone:

- Inferring model parameters (e.g material properties) from noisy measurements coming from experiments
- Monitoring existing structures and detecting hidden defects which might cause failure
- Upscaling mechanical properties from a small number of high-fidelity simulations

For all of the above, deterministic approaches are often ill-posed and uninformed, while conventional Bayesian inference is extremely slow (e.g forward Monte Carlo, MCMC).

## Piecewise Deterministic Markov Processes (PDMPs)

When the likelihood function requires solving a PDE, standard MCMC can become impractical: most steps are wasted due to random-walk behavior, and each likelihood/gradient evaluation is costly. **PDMPs** provide an appealing alternative because they can explore posteriors with fewer correlated samples per likelihood evaluation. The key idea is to explore the inference space with *deterministic dynamics* that can however *change directions at random times*. With this we can explore whole lines in parameter space rather than single points:

<div class="row justify-content-center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pdmp/rwm.gif" title="Conventional MCMC" class="img-capped rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pdmp/zz.gif" title="Zig-zag sampler" class="img-capped rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pdmp/bps.gif" title="Bouncy Particle sampler" class="img-capped rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Inferring two PDE parameters with conventional Random Walk MCMC (left), the Zig-zag sampler (middle), and the Bouncy Particle sampler (right) 
</div>

I collaborate with [Joris Bierkens](https://scholar.google.nl/citations?hl=nl&user=JlAi6VAAAAAJ) and [Hanne Kekkonen](https://scholar.google.nl/citations?user=wxsxtq4AAAAJ&hl=nl), the statistical learning experts who develop these samplers, in applying PDMPs for real-world problems.
In a recent publication {% cite riccius2026piecewisedeterministicmarkovprocesses %} we develop an approach for using PDMPs to infer PDE parameters (e.g. material properties with a FEM model and experimental measurements).
Since FEM is much more messy than the usual well-behaved distributions Joris and Hanne use to develop the samplers, we came up with a way to still make it work by combining Poisson Thinning with a GP-based surrogate model that approximates the gradient of the log-target.
Our approach extends the applicability of PDMPs to a much wider range of problems, and for PDE parameters in computational mechanics we actually find our PDMPs outperform state-of-the-art NUTS (*No U-Turn Sampler*) implementations:

<div class="row">
    <div class="col-12 col-md-8 col-lg-6 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="/assets/research/pdmp/ess_comparison.png" title="PDMP ESS comparison" class="img-capped rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Effective sample size per likelihood evaluation (higher is better). Comparison between a simple Random Walk Metropolis (RMW), the No U-Turn Sampler (NUTS) and our Zig-Zag sampler (ZZ) and Bouncy Particle sampler (BPS). We consistently outperform NUTS for a wide range of dimensionalities, which means we require much fewer expensive FEM model evaluations but still obtain accurate inference results.
</div>

You can play with our approach by using the following interactive plot. You can move the means of the Gaussian mixture that makes up the ground truth posterior to generate more challenging multi-modal problems:

<div id="pdmp-sandbox" class="card p-3 mb-4">
    <h3 class="mb-2">A tiny PDMP sandbox</h3>
    <p class="text-muted mb-3">
        Try out our surrogate-enhanced Zig-zag sampler implementation in a non-Gaussian problem: 
    </p> 
    {% include research-pdmp.html %}
</div>
