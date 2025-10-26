---
layout: page
title: GeoRad3D
description: 3D modeling and visualization of in-situ radiometric measurements (X, Y, Z, counts per minute) with volumetric interpolation.
img: assets/img/projects/example_georad3d.png
importance: 1
category: work
related_publications: false
permalink: /projects/georad3d/
links:
  - name: GitHub
    url: https://github.com/MaxSC4/GeoRad3d
---

**GeoRad3D** is a workflow for **3D modeling and visualization of radiometric data** measured on outcrops or surfaces (X, Y, Z, counts per minute).
It generates continuous **volumetric representations** from discrete points using 3D interpolation, and provides clean figures for interpretation and reporting.

<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/z_sweep.gif" title="GeoRad3D — volumetric model of radioactivity" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Figure 1. Example of a Z-slice from a interpolated volume from surface radiometric measurements.
</div>

---

## Key features
- **Input:** point measurements with coordinates *(X, Y, Z)* and **counts per minute (CPM)**
- **3D interpolation:** builds a **continuous volume** from discrete points (solid model)
- **Section views & slicing:** extract cross-sections and profiles for analysis and figures
- **Export:** save figures and datasets for further processing / archiving
- **Reproducible workflow:** from raw field data to publication-ready visuals

---

## Typical workflow
1. **Load** field measurements (CSV or similar tabular data)
2. **Preprocess** (filters, normalization, grid/domain definition)
3. **Interpolate in 3D** to create a volumetric model
4. **Visualize & slice** the volume; extract quantitative summaries
5. **Export** figures / data products

---

## Use cases
- Outcrop-scale **radioactivity mapping**
- Comparison of **facies or lithological units** via CPM distribution
- **Teaching & reproducible research** in applied geosciences

---

### Repository
- 💻 **GitHub:** <https://github.com/MaxSC4/GeoRad3d>
