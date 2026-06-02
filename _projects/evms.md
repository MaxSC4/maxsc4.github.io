---
layout: page
title: EVMS
description: Volumetric inversion of geological radioactivity from surface gamma measurements.
img: assets/img/projects/evms_cover_card.png
importance: 1
category: work
related_publications: false
permalink: /projects/evms/
links:
  - name: App
    url: https://evms-geops.streamlit.app/
  - name: GitHub
    url: https://github.com/MaxSC4/evms
---

**EVMS** is a scientific Python framework for reconstructing a **3D subsurface source-intensity field** from **surface gamma measurements**. It combines a sparse forward model, geological regularization, and an interactive **Streamlit** interface to support reproducible inversion workflows.

Developed for geological applications, EVMS is designed to move from sparse radiometric observations to interpretable volumetric models, diagnostics, and exportable outputs suitable for analysis and communication.

<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/evms_accueil.png" title="EVMS overview" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  EVMS overview image.
</div>

<style>
  .evms-actions {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 0.9rem;
    margin: 2rem 0 2.25rem;
  }

  .evms-action {
    display: inline-flex;
    align-items: center;
    gap: 0.7rem;
    padding: 0.95rem 1.2rem;
    border: 1px solid #4b6335;
    border-radius: 1rem;
    text-decoration: none;
    background: linear-gradient(135deg, #556f3a, #6f8d4a);
    box-shadow: 0 14px 28px rgba(53, 67, 38, 0.16);
    color: #f8f5ed;
    transition:
      transform 0.18s ease,
      box-shadow 0.18s ease,
      background 0.18s ease,
      border-color 0.18s ease;
  }

  .evms-action:hover {
    transform: translateY(-2px);
    box-shadow: 0 18px 34px rgba(53, 67, 38, 0.18);
    border-color: #394d26;
    background: linear-gradient(135deg, #485f31, #607b3f);
    color: #fffdf7;
    text-decoration: none;
  }

  .evms-action-icon {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 2.4rem;
    height: 2.4rem;
    border-radius: 999px;
    background: rgba(53, 67, 38, 0.12);
    font-size: 1rem;
    flex: 0 0 auto;
  }

  .evms-action .evms-action-icon {
    background: rgba(255, 250, 240, 0.16);
  }

  .evms-action-copy {
    display: flex;
    flex-direction: column;
    line-height: 1.1;
  }

  .evms-action-label {
    font-weight: 700;
    font-size: 0.98rem;
  }

  .evms-action-meta {
    font-size: 0.78rem;
    opacity: 0.78;
    margin-top: 0.22rem;
  }

  .evms-citation {
    max-width: 60rem;
    margin: 0 auto 2.4rem;
    padding: 0.95rem 1.15rem;
    border: 1px solid rgba(85, 111, 58, 0.28);
    border-radius: 1rem;
    background: rgba(111, 141, 74, 0.08);
    color: inherit;
    text-align: center;
  }

  .evms-citation p {
    margin: 0;
  }

  .evms-citation strong {
    color: #3f552a;
  }
</style>

<div class="evms-actions">
  <a href="https://evms-geops.streamlit.app/" class="evms-action" target="_blank" rel="noopener noreferrer">
    <span class="evms-action-icon"><i class="fa-solid fa-arrow-up-right-from-square"></i></span>
    <span class="evms-action-copy">
      <span class="evms-action-label">Launch EVMS</span>
      <span class="evms-action-meta">Open the Streamlit application</span>
    </span>
  </a>
  <a href="https://github.com/MaxSC4/evms" class="evms-action" target="_blank" rel="noopener noreferrer">
    <span class="evms-action-icon"><i class="fa-brands fa-github"></i></span>
    <span class="evms-action-copy">
      <span class="evms-action-label">GitHub Repository</span>
      <span class="evms-action-meta">Browse code and project files</span>
    </span>
  </a>
</div>

<div class="evms-citation">
  <p>Soares Correia, M. (2026). <em>EVMS: Volumetric inversion of geological radioactivity from surface gamma measurements</em> (Version 0.1.1) [Computer software]. GEOPS — Géosciences Paris-Saclay (UMR 8148, CNRS), Université Paris-Saclay. https://doi.org/10.5281/zenodo.18586941</p>
</div>

## Key features

EVMS is not a generic visualizer with an inversion label attached to it. The repository implements a full volumetric inverse problem in Python, beginning with a masked voxel discretization of the subsurface and ending with numerical diagnostics, calibrated outputs, and textured three-dimensional exports. The codebase is organized around a scientific core in `src/evms`, a multi-page Streamlit interface for applied workflows, and a test suite that exercises the forward model, the regularization machinery, the inverse solver, the calibration stage, the grid geometry, and the fracture logic.

The geometry of the problem is represented by a `VoxelGrid` dataclass. In the implementation, the inversion domain is defined by an origin, isotropic or anisotropic voxel spacing, grid dimensions, and a boolean activity mask that decides which cells belong to the model. EVMS can start either from a direct `npy` mask or from an `obj` mesh, which is voxelized through `trimesh` into an active domain. Once the mask is known, EVMS works only on active voxels, computes their centers, builds neighbor relations, and stores the inverse problem on that reduced domain rather than on the full bounding box. That choice matters because it avoids solving for empty space and keeps the numerical system sparse from the start.

The physical model coded in `forward.py` treats each voxel as a source cell whose contribution to a surface observation decays both geometrically and exponentially. For a measurement position $\mathbf{x}$ and a source intensity field $S(\mathbf{r})$, the conceptual model is

$$
M(\mathbf{x}) = \int_V S(\mathbf{r})\,G(\mathbf{x}, \mathbf{r})\,e^{-\mu \lVert \mathbf{x}-\mathbf{r} \rVert}\,dV + \varepsilon,
$$

with the kernel

$$
G(\mathbf{x}, \mathbf{r}) = \frac{1}{\lVert \mathbf{x}-\mathbf{r} \rVert^2 + \epsilon}.
$$

In the actual code this is discretized voxel by voxel. If $\mathbf{x}_i$ is the $i$-th measurement point, $\mathbf{r}_j$ is the center of the $j$-th active voxel, and $\Delta V = \Delta x\,\Delta y\,\Delta z$ is the voxel volume, then EVMS assembles a sparse matrix $A$ with coefficients

$$
A_{ij} =
\begin{cases}
\dfrac{\Delta V}{d_{ij}^2 + \epsilon}\,e^{-\mu d_{ij}}, & d_{ij} < R_{\max}, \\
0, & d_{ij} \ge R_{\max},
\end{cases}
\qquad d_{ij} = \lVert \mathbf{x}_i - \mathbf{r}_j \rVert.
$$

This truncation at $R_{\max}$ is not cosmetic. In the implementation, EVMS builds a `cKDTree` over voxel centers and queries only neighbors inside the influence radius. The result is a CSR sparse operator rather than a dense matrix, which is precisely what makes the model scalable enough for interactive work on nontrivial voxel domains. In the Streamlit workspace the attenuation coefficient is not left unconstrained either. The interface explicitly recommends a first-pass range of $8$ to $16\;\mathrm{m}^{-1}$, together with an influence radius chosen on the order of three to six attenuation lengths, using a default close to $4/\mu$. That guidance is encoded in the controls and is therefore part of the actual modeling philosophy of the tool.

Once the forward operator is assembled, EVMS solves a regularized inverse problem rather than a naive back-projection. The target field $\hat{\mathbf{S}}$ is defined as the minimizer of

$$
\hat{\mathbf{S}} = \arg\min_{\mathbf{S}} \left\lVert A\mathbf{S} - \mathbf{M} \right\rVert_2^2 + \lambda \left\lVert L\mathbf{S} \right\rVert_2^2,
$$

where $\mathbf{M}$ is the measurement vector and $L$ is the regularization matrix assembled in `regularization.py`. In code, EVMS does not solve the corresponding normal equations directly. Instead, it augments the system into

$$
\begin{bmatrix}
A \\
\sqrt{\lambda}\,L
\end{bmatrix}
\mathbf{S}

=
\begin{bmatrix}
\mathbf{M} \\
\mathbf{0}
\end{bmatrix},
$$

and solves that least-squares problem with `scipy.sparse.linalg.lsqr`. This is a practical and robust choice for sparse problems of the kind EVMS constructs.

The regularization is one of the most interesting parts of the repository because it is written as a graph penalty on voxel neighbors rather than as a purely abstract smoothing term. For neighboring voxels $j$ and $j'$, EVMS penalizes differences according to

$$
\lVert L\mathbf{S} \rVert_2^2
=
\sum_{j \sim j'} w(j,j')\,(S_j - S_{j'})^2.
$$

In the library implementation, the edge weight is decomposed as the sum of a layer term and a fracture term,

$$
w(j,j') = w_{\mathrm{layer}}(j,j') + w_{\mathrm{fract}}(j,j').
$$

The layer contribution is binary in the current code: it is equal to one when both voxels belong to the same layer label and zero otherwise. The fracture contribution is computed only when the segment connecting two voxel centers intersects a finite rectangular fracture patch. The fracture model is not a vague placeholder. The repository contains explicit geometry for plane-segment intersection, in-rectangle testing, and a longitudinal modulation law based on the in-plane coordinate $s$:

$$
\rho(s) = \rho_0 + \rho_1 \frac{|s|}{L/2},
\qquad
D(s) = \frac{L}{\max(\rho(s), \rho_{\min})},
\qquad
g(D) = \frac{D}{D_{\mathrm{ref}}},
$$

with the implemented crossing weight returned as

$$
w_{\mathrm{fract}} = \alpha\,g(D).
$$

The current Streamlit inversion workspace initializes all voxels with the same layer label, so the baseline application behaves like a homogeneous geological smoothing model unless fracture patches are supplied. That detail matters because it distinguishes what the core EVMS library is capable of from what the current front-end uses by default.

Parameter selection is also handled programmatically rather than by hand-waving. The inversion module implements two strategies for choosing $\lambda$. The first is an L-curve search on a logarithmic grid, where EVMS computes residual norms and regularization norms over candidate values and then uses a curvature heuristic in log space to select the corner of the curve. The second is holdout cross-validation, in which measurement rows are randomly split into training and holdout subsets and the chosen $\lambda$ is the one that minimizes holdout RMSE. The code also contains a second grid search over $\mu$ and $R_{\max}$, making it possible to evaluate forward-physics parameters against either residual norm or holdout performance. This is a strong point of EVMS because it moves the workflow away from arbitrary tuning and toward measurable model selection criteria.

The calibration stage is likewise explicit in the implementation. EVMS first reconstructs a source field in relative units. If calibration points are available, the code matches those physical observations to the nearest active voxels through another `cKDTree`, samples the reconstructed values, and fits a linear model

$$
y_{\mathrm{phys}} = a\,x_{\mathrm{rel}} + b,
$$

with an option to force $b = 0$ if a gain-only relation is preferred. The fitted model returns the gain, the offset, the coefficient of determination $R^2$, and the sample count. The calibrated field is then produced by direct application of that affine map to the reconstructed voxel values. In practice this means EVMS is not limited to relative anomaly mapping; it can be pushed toward physically interpretable output when field calibration data exist.

The diagnostics in the workspace are more than decorative charts. EVMS computes the residual vector $\mathbf{r} = \mathbf{M} - A\hat{\mathbf{S}}$, the data misfit norm $\lVert A\hat{\mathbf{S}} - \mathbf{M} \rVert_2$, and the regularization norm $\lVert L\hat{\mathbf{S}} \rVert_2$. If holdout diagnostics are enabled, the code refits the model on a training subset and evaluates holdout RMSE, MAE, and $L^2$ error on unseen measurements. It then builds a trust score from two dimensionless ratios. The fit component is

$$
\mathrm{fit\_score} = \frac{1}{1 + \lVert A\hat{\mathbf{S}} - \mathbf{M} \rVert_2 / \lVert \mathbf{M} \rVert_2},
$$

and, when holdout mode is active, the generalization component is

$$
\mathrm{holdout\_score} = \frac{1}{1 + \mathrm{RMSE}_{\mathrm{holdout}} / \sigma(\mathbf{M})}.
$$

The final trust score is the mean of the available components and is mapped to qualitative levels that the interface labels as High, Moderate, or Low. This is one of the reasons EVMS feels like a scientific workflow rather than a one-click rendering app: the code insists on reporting how much confidence the inversion deserves.

The presentation layer is also tightly coupled to the computation. The Streamlit workspace includes direct upload of measurement CSV files of the form `x,y,z,value`, optional fracture JSON files, optional calibration CSV files, and either a voxel mask or a surface mesh for the inversion domain. Once the computation is done, EVMS exposes planar slices through the reconstructed volume, histograms of source intensity, a three-dimensional Plotly scatter representation of active voxels colored by reconstructed values, a three-dimensional residual map at measurement points, and a residual histogram. The application therefore makes the inverse problem inspectable both numerically and spatially, which is essential when the goal is geological interpretation rather than pure optimization.

Finally, the export path in `io.py` is more sophisticated than a simple array dump. EVMS can save the reconstructed field as a masked three-dimensional `npy` volume, but when the input domain comes from an OBJ mesh it can also transfer voxel values back to the mesh. The repository includes a box-projection UV atlas, tile-aware vertex duplication, rasterization of scalar values into a texture image, and export of a viewer-agnostic `OBJ + MTL + PNG` bundle. In other words, EVMS is able to move from inverse modeling to an interpretable textured three-dimensional geological object that can be shared outside the inversion environment.

<div class="row justify-content-sm-center">
  <div class="col-sm-10 mt-4 mt-md-2">
    {% include figure.liquid loading="eager" path="assets/img/projects/evms_results_example.png" title="EVMS inversion results example" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Example EVMS output showing how the inversion is presented as an interpretable three-dimensional result rather than as an abstract numerical field alone.
</div>

What makes EVMS compelling is precisely this chain of implementation choices. The repository does not stop at a conceptual inverse problem. It encodes a sparse attenuation-based forward model, a geology-aware graph regularizer, parameter-search logic, calibration to physical units, trust-oriented diagnostics, and exportable geometric outputs in one coherent workflow. That is why EVMS is interesting: it treats volumetric radioactivity inversion as a scientific modeling problem, not as a visualization trick dressed in geoscience vocabulary.
