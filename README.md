# GPU Frame Rendering Research Notebook

A no-code, theory-first research repository for understanding how modern GPUs render high-speed game frames, from foundational linear algebra to deep silicon-level execution and display synchronization dynamics.

---

## Scope & Methodology

- **Format:** Markdown notes + LaTeX derivations
- **Approach:** Build from first principles, then progressively map equations to fixed-function and programmable hardware stages
- **Outcome:** A rigorous, cross-linked textbook/lab-notebook for graphics math, rendering pipelines, and GPU architecture

---

## Research Roadmap

### Module 1 — Vector Spaces & Affine Transformations
**Core focus:** Coordinate systems, basis changes, homogeneous coordinates, and the full model-view-projection (MVP) pipeline.

#### Key derivations to include
- Coordinate transform chain:
  $$
  \mathbf{p}_{clip} = \mathbf{P}\,\mathbf{V}\,\mathbf{M}\,\mathbf{p}_{model}
  $$
- Homogeneous point representation:
  $$
  \mathbf{p} = (x, y, z, 1)^T
  $$
- Perspective divide into NDC:
  $$
  (x_{ndc},y_{ndc},z_{ndc}) = \left(\frac{x_c}{w_c},\frac{y_c}{w_c},\frac{z_c}{w_c}\right)
  $$
- Canonical perspective projection matrix (right-handed convention example):
  $$
  \mathbf{P} = \begin{bmatrix}
  \frac{1}{\tan(\theta/2)\,a} & 0 & 0 & 0 \\
  0 & \frac{1}{\tan(\theta/2)} & 0 & 0 \\
  0 & 0 & \frac{f+n}{n-f} & \frac{2fn}{n-f} \\
  0 & 0 & -1 & 0
  \end{bmatrix}
  $$

#### Questions / papers / whitepapers to research
1. How do depth precision and near-plane placement interact in finite vs. reversed-Z projections?
2. Which matrix/storage conventions differ across APIs (OpenGL, DirectX, Vulkan), and how do they alter derivations?
3. Read: *Real-Time Rendering* (Akenine-Möller et al.) MVP chapters + API spec projection conventions.

---

### Module 2 — The Rasterization Stage & Discrete Mathematics
**Core focus:** Triangle coverage tests, edge equations, barycentrics, and tile/bin-based hardware traversal.

#### Key derivations to include
- Edge function form:
  $$
  E_{ij}(x,y) = (y_i - y_j)x + (x_j - x_i)y + (x_i y_j - x_j y_i)
  $$
- Inside-triangle test via signed edge consistency:
  $$
  E_{01}(p) \ge 0,\; E_{12}(p) \ge 0,\; E_{20}(p) \ge 0
  $$
- Barycentric coordinate normalization:
  $$
  \lambda_k = \frac{A_k}{A_{\triangle}}, \quad \lambda_0+\lambda_1+\lambda_2=1
  $$
- Tile occupancy reasoning:
  $$
  N_{tiles} = \left\lceil \frac{W}{T_x} \right\rceil \left\lceil \frac{H}{T_y} \right\rceil
  $$

#### Questions / papers / whitepapers to research
1. How do top-left fill rules guarantee crack-free shared edges?
2. What are the hardware tradeoffs between immediate-mode and tile-based/deferred rasterization?
3. Read: Pineda (1988) edge-function rasterization + vendor docs on tile/bin raster pipelines.

---

### Module 3 — Shading, Calculus & Optics
**Core focus:** Interpolation correctness, physically based shading, and radiometric light transport.

#### Key derivations to include
- Perspective-correct interpolation:
  $$
  a = \frac{\sum_i \lambda_i \left(a_i / w_i\right)}{\sum_i \lambda_i \left(1 / w_i\right)}
  $$
- Reflectance equation:
  $$
  L_o(\mathbf{x},\omega_o)=\int_{\Omega} f_r(\mathbf{x},\omega_i,\omega_o)L_i(\mathbf{x},\omega_i)(\mathbf{n}\cdot\omega_i)\,d\omega_i
  $$
- Cook-Torrance BRDF:
  $$
  f_r=\frac{D(h)\,F(\omega_i,h)\,G(\omega_i,\omega_o,h)}{4(\mathbf{n}\cdot\omega_i)(\mathbf{n}\cdot\omega_o)}
  $$

#### Questions / papers / whitepapers to research
1. How do normal mapping and tangent-space transforms impact BRDF energy behavior?
2. Which assumptions in microfacet models fail for real-time approximations at extreme frame budgets?
3. Read: Cook & Torrance (1982), Kajiya (1986), and SIGGRAPH PBR course notes.

---

### Module 4 — Silicon Micro-architecture & Parallelism
**Core focus:** SIMT/SIMD execution models, scheduling, memory hierarchy, and divergence mathematics.

#### Key derivations to include
- Effective warp utilization:
  $$
  U = \frac{\text{active lanes}}{\text{warp width}}
  $$
- Occupancy estimate (conceptual):
  $$
  \text{occupancy} = \frac{\text{resident warps per SM}}{\text{max warps per SM}}
  $$
- Throughput bound intuition:
  $$
  T_{kernel} \approx \min\left(T_{compute},\,T_{memory},\,T_{issue}\right)
  $$

#### Questions / papers / whitepapers to research
1. How do register pressure and shared memory allocation jointly cap occupancy?
2. What exact mechanisms do modern schedulers use to hide latency under branch divergence?
3. Read: NVIDIA architecture whitepapers (Pascal/Volta/Turing/Ampere/Ada), AMD GCN/RDNA whitepapers, and SIMT execution model docs.

---

### Module 5 — Ultra-High Refresh Rate Synchronizations
**Core focus:** Frame pacing, scanout timing, VRR behavior, and end-to-end latency budgets.

#### Key derivations to include
- Frame interval from refresh rate:
  $$
  \Delta t = \frac{1}{f_{refresh}}
  $$
- Queueing-style end-to-end latency decomposition:
  $$
  L_{total} = L_{input} + L_{CPU} + L_{GPU} + L_{queue} + L_{scanout} + L_{pixel}
  $$
- Scanout position time (idealized):
  $$
  t_{row}(r)=\frac{r}{R}\,\Delta t
  $$
  where $r$ is row index and $R$ is total scanned rows.

#### Questions / papers / whitepapers to research
1. How do VRR/G-Sync/FreeSync protocols alter stutter and tearing probabilities under variable render time?
2. What pacing heuristics minimize perceived jitter at 240 Hz+ under inconsistent frame times?
3. Read: vendor VRR technical documentation + latency analysis papers (e.g., beam-racing, low-latency presentation strategies).

---

## Repository Structure (Template)

```text
GPU-Working/
├── README.md
├── references/
│   ├── papers/
│   │   ├── graphics-foundations.md
│   │   ├── rasterization-classics.md
│   │   ├── shading-light-transport.md
│   │   └── gpu-architecture-whitepapers.md
│   └── glossary.md
├── modules/
│   ├── 01-vector-spaces-affine/
│   │   ├── 00-overview.md
│   │   ├── 01-linear-algebra-primer.md
│   │   ├── 02-coordinate-systems.md
│   │   ├── 03-homogeneous-coordinates.md
│   │   ├── 04-projection-matrix-derivations.md
│   │   └── 05-depth-precision-notes.md
│   ├── 02-rasterization-discrete/
│   │   ├── 00-overview.md
│   │   ├── 01-edge-functions.md
│   │   ├── 02-barycentric-geometry.md
│   │   ├── 03-coverage-fill-rules.md
│   │   └── 04-tile-based-hardware.md
│   ├── 03-shading-calculus-optics/
│   │   ├── 00-overview.md
│   │   ├── 01-perspective-correct-interpolation.md
│   │   ├── 02-radiometry-photometry.md
│   │   ├── 03-brdf-cook-torrance.md
│   │   └── 04-light-transport-theory.md
│   ├── 04-microarchitecture-parallelism/
│   │   ├── 00-overview.md
│   │   ├── 01-simt-warps-wavefronts.md
│   │   ├── 02-scheduling-latency-hiding.md
│   │   ├── 03-registers-occupancy.md
│   │   ├── 04-cache-hierarchy.md
│   │   └── 05-divergence-math.md
│   └── 05-refresh-rate-sync/
│       ├── 00-overview.md
│       ├── 01-frame-pacing-math.md
│       ├── 02-vrr-gsync-freesync.md
│       ├── 03-display-pipeline-latency.md
│       └── 04-240hz-plus-case-studies.md
└── notebook-index/
    ├── weekly-log.md
    ├── open-questions.md
    └── derivation-index.md
```

---

## Research Workflow (Suggested)

1. Pick one module and define precise learning outcomes.
2. Derive equations manually and document assumptions/coordinate conventions.
3. Cross-reference theory with one architecture whitepaper and one classic paper.
4. Summarize unresolved questions into `notebook-index/open-questions.md`.
5. Iterate until each module has: definitions, full derivations, literature links, and architecture mapping notes.
