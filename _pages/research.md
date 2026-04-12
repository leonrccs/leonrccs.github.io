---
layout: page
title: research
permalink: /research/
description:
nav: true
nav_order: 2
horizontal: false
---

<div class="projects">
  {% assign sorted_projects = site.research | sort: "importance" %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
</div>
