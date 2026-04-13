---
layout: about
title: about
permalink: /
subtitle: PhD researcher at <a href='#'>TU Delft</a>.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
#  more_info: >
#    <p>computational mechanics group</p>
#    <p>faculty of civil engineering and geosciences</p>
#    <p>delft university of technology</p>

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: true
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---
 
I am building scalable Bayesian inference pipelines for physics-based models.
I develop methodology at the intersection of advanced Monte Carlo methods (PDMP samplers), Gaussian processes, and computational mechanics to solve inverse problems in material model calibration.
My wider interests span generative modeling, simulation-based inference, and the fusion of machine learning with numerical methods for materials discovery and design.

## Research

**[PDMP sampling](/research/bayes/)** — Standard MCMC wastes most evaluations on random-walk exploration. Piecewise Deterministic Markov Processes travel along deterministic trajectories and switch direction at random times, sampling whole lines in parameter space rather than single points. Applied to PDE parameter inference, our surrogate-enhanced Zig-Zag sampler and Bouncy Particle sampler consistently outperform the NUTS in terms of accuracy per cost. Try it yourself in the [interactive PDMP sandbox](/research/bayes/#pdmp-sandbox).

**[MCMC-guided active learning](/research/active_learning/)** — Surrogate models make Bayesian inference tractable, but only if training data lands where the posterior lives. We couple GP surrogates directly to the MCMC chain: new FEM evaluations are triggered only where the surrogate is uncertain, focusing compute precisely on the posterior support. The result: accurate inference with a fraction of the forward model evaluations.

**[Physics-informed turbulence closure](/research/pi_tbnn/)** — RANS closures break down for flows with curvature and separation. We extend the Tensor Basis Neural Network with a richer feature set and embed realizability constraints as a training-loss penalty, achieving a 70% reduction in anisotropy tensor error over the k-ω baseline.
