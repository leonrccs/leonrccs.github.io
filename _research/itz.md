---
layout: page
title: probabilistic itz characterization
description:
img: assets/img/project_covers/itz.png
importance: 4
category: applications
related_publications: false
---

The stiffness and cracking behaviour of concrete are strongly influenced by the **interfacial transition zone (ITZ)**: the thin shell of cement paste, only tens of micrometres thick, that surrounds every aggregate.
Mesoscale models of concrete need the mechanical properties of this shell as input, yet they are poorly characterized and usually assigned through ad hoc reduction factors applied to the bulk paste.
Reported estimates scatter widely between studies and rarely come with a quantified uncertainty.

_This work is a draft manuscript that has not been submitted yet._

## From micro-cantilever tests to mesoscale predictions

Together with Iuri Rocha, Erik Schlangen and Frans van der Meer, I developed a workflow that calibrates a homogenized ITZ model against micro-cantilever bending specimens, and returns its stiffness descriptors as **posterior distributions** instead of single values.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/overview.png" title="Overview of the workflow" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of the workflow: the homogenized ITZ model is calibrated against digital specimens, and the calibrated descriptors are propagated through an RVE of several aggregates to the quantities of interest.
</div>

The workflow has three steps:

1. **Digital specimens.** Micro-cantilever beams of cement paste bonded to an aggregate are constructed from segmented X-ray tomography images. A fully resolved finite-element model of each one supplies surface displacements at a controlled noise level, standing in for a physical experiment.
2. **Calibration.** The ITZ is described by a homogenized model with only three stiffness descriptors: a modulus ratio, a recovery length scale and the far-field stiffness. We infer them either as a point estimate (MAP), a Laplace approximation, or by sampling the full posterior.
3. **Forward propagation.** The calibrated descriptors are pushed through an RVE of several aggregates, by linearization or Monte Carlo, to obtain distributions of the macroscopic axial stress, the maximum von Mises stress and the maximum principal strain.

The inference is posed as a Bayesian inverse problem with a Kennedy–O'Hagan model-discrepancy term that absorbs the systematic error of the simplified ITZ model.
Each likelihood evaluation requires a finite-element solve, so we sample the posterior with the surrogate-assisted bouncy particle sampler from [our PDMP work]({{ '/research/bayes/' | relative_url }}) — its first application to a real-world engineering problem.

## Pooled vs. hierarchical calibration

Micro-cantilevers of different aggregates behave appreciably differently, a manifestation of the intrinsic spatial randomness of hardened cement paste.
A **pooled** model fits one descriptor vector to all specimens and therefore conflates model error with this material variability.
A **hierarchical** model instead treats the specimens as draws from a common population, and separates what the finite data leave unresolved (which shrinks as more specimens are added) from the aggregate-to-aggregate variability (which does not).
To keep sampling its much larger state space affordable, we extend the sampler to a *local* bouncy particle sampler that exploits the sparse structure of the hierarchical posterior, such that a candidate event still costs a single finite-element solve.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/theta_marginals.png" title="Prior and posterior marginals of the ITZ descriptors" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Prior and posterior marginals of the ITZ descriptors for the pooled posterior (orange) and the hierarchical predictive (blue). The MAP point (brown) lies in a low-probability region, which shows the merit of a full probabilistic treatment over a point estimate.
</div>

The pooled and hierarchical models agree closely on the descriptor means, but the hierarchical predictive is broader and gives a more faithful picture of the material.
The calibration also places the ITZ thickness above the 50 µm commonly assumed in the literature.

<div class="row">
    <div class="col-12 col-md-6 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/field_comparison.png" title="Modulus field of pooled and hierarchical models" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Stiffness profile away from the aggregate for the pooled posterior and the hierarchical predictive. The hierarchical predictive captures the spread between individual specimens, whereas the pooled band is noticeably too narrow, most of all close to the interface.
</div>

## Why it matters at the mesoscale

In a concrete mesostructure every aggregate carries its own, unobserved ITZ, so a mesoscale prediction needs the predictive distribution of a *new* aggregate rather than one calibrated descriptor vector.
Propagated through the RVE, the hierarchical predictive yields broader distributions of the macroscopic stress, maximum von Mises stress and maximum principal strain.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/forward_uq.png" title="Forward propagation of pooled and hierarchical posteriors" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Propagation of the pooled (orange) and hierarchical (blue) posteriors to the macroscopic stress, maximum von Mises stress and maximum principal strain. Dashed lines mark the empirical means.
</div>

Even in the linear-elastic regime the two models behave markedly differently:

<div class="row justify-content-center">
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/locs_pooled.png" title="Maximum stress locations, pooled" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/itz/locs_hierarchical.png" title="Maximum stress locations, hierarchical" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Locations of the maximum von Mises stress for the pooled posterior (left) and the hierarchical predictive (right). The pooled model confines the stress concentration to a single region, while the hierarchical predictive spreads it over many locations.
</div>

We expect this difference to be amplified beyond the elastic regime, where crack nucleation and propagation are highly sensitive to local stiffness contrasts.
Testing this in the non-linear regime, and applying the workflow to physical micro-cantilever measurements, are the natural next steps.
