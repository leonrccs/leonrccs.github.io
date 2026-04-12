---
layout: page
title: physics-informed turbulence closure
description:
img: assets/img/project_covers/pi_tbnn.png
importance: 3
category: applications
related_publications: true
---

Reynolds-Averaged Navier-Stokes (RANS) simulations are the workhorse of industrial fluid mechanics: they are orders of magnitude cheaper than scale-resolving methods, but their accuracy depends entirely on the turbulence closure model.
Standard linear eddy viscosity models (LEVMs) assume the Reynolds stress tensor is proportional to the mean rate of strain — an assumption that breaks down for flows with curvature, separation, or secondary motions.

## The barycentric map

Every state of turbulence can be placed in a triangular map whose vertices represent three limiting cases: one-component (1C), axisymmetric two-component (2C), and isotropic three-component (3C) turbulence.
The barycentric map, together with the RGB colormap below, provides a convenient way to visualize where in this space a turbulence model actually predicts.

<div class="row justify-content-center">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pi_tbnn/colormap.png" title="Barycentric colormap" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The barycentric colormap: each color corresponds to a unique turbulence state. Red = one-component, green = two-component axisymmetric, blue = three-component isotropic.
</div>

A square duct flow is a canonical case where LEVMs notoriously fail: secondary corner vortices driven by turbulence anisotropy are entirely absent from LEVM predictions.
The barycentric maps make this failure visible at a glance.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pi_tbnn/barmap_rans.png" title="RANS k-omega barycentric map" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pi_tbnn/barmap_dns.png" title="DNS barycentric map" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Barycentric maps for square duct flow: standard k-ω RANS (left) vs DNS (right). The RANS model predicts near-isotropic turbulence throughout, completely missing the strong anisotropy near the walls.
</div>

## Physics-Informed Tensor Basis Neural Network (PI-TBNN)

The Tensor Basis Neural Network (TBNN) of Ling et al. learns to predict the anisotropy tensor as a linear combination of ten integrity basis tensors, guaranteeing frame invariance by construction.
In {% cite riccius2023pitbnn %} we extend this framework in two ways:

**Extended feature set.** We show analytically that for 2D-homogeneous flows, two of the five standard TBNN invariants vanish identically — they carry no information. We augment the input with gradients of the turbulent kinetic energy and pressure, giving 17 features in total.

**Realizability constraints as inductive bias.** Physical turbulence states must lie inside the barycentric triangle. Rather than post-processing predictions onto the boundary (as in Ling et al.), we incorporate the six realizability inequalities directly as a penalty term in the training loss. Violating predictions are pushed back inside the physical domain during training.

The combined model, PI-TBNN, achieves roughly **70% reduction in anisotropy tensor RMSE** compared to the k-ω baseline and **50% reduction** compared to the original TBNN of Ling et al., tested on geometries and Reynolds numbers not seen during training.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pi_tbnn/ph_prediction.png" title="Anisotropy tensor prediction for periodic hills" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Anisotropy tensor predictions for periodic hills flow (unseen test case). Columns: LEVM k-ω, PI-TBNN with 3 features (FS1), PI-TBNN with 17 features (FS[1-3]), DNS ground truth. The full feature set recovers the 1C turbulence regions and axisymmetric expansion that the LEVM misses entirely.
</div>

## The gap between anisotropy and mean fields

A key finding — and an honest limitation — is that improved anisotropy tensor predictions do not reliably translate into improved mean velocity and pressure fields.
When the PI-TBNN anisotropy tensor is used as a source term in the RANS equations, the resulting velocity fields are better than k-ε on geometries with separation, but the k-ω model remains the best-performing LEVM overall.
Strikingly, even using the DNS anisotropy tensor directly fails to beat k-ω on periodic hills — a result also reported independently in the literature.

<div class="row">
    <div class="col-12 col-md-10 mx-auto mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/research/pi_tbnn/ph_propagation.png" title="Mean velocity profiles" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Streamwise velocity profiles at several x-locations for periodic hills (left) and curved backward-facing step (right). Despite a large improvement in anisotropy tensor accuracy, the mean velocity field from the PI-TBNN does not consistently outperform k-ω.
</div>

This suggests that the most productive direction for data-driven turbulence modelling is to target mean-field quantities directly rather than the Reynolds stresses as an intermediate quantity.
