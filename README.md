<div align="center">

# μSLAM

### Uncertainty-Driven 3D Gaussian Splatting for Robust Real-Time RGB-D SLAM

*Anonymous review repository for IEEE T-ASE submission T-ASE-2025-4521*

[**Project Page**](https://anoymous-code.github.io/muSLAM/) · [Demos](#demos) · [Results](#results) · [Configurations](#configurations--evaluation-protocol) · [Code Release Plan](#code-release-plan)

<img src="assets/teaser.png" width="92%" alt="Hybrid uncertainty-aware map: photorealistic 3DGS field with per-primitive confidence visualization"/>

*Our hybrid map couples a photorealistic 3DGS field (left) with a per-primitive **confidence** field (right, blue: low, red: high). Embedded **shadow Gaussians** (white circles) provide a globally consistent geometric backbone.*

</div>

---

## What is μSLAM?

μSLAM is a **real-time RGB-D SLAM system** (>30 FPS on a single GPU) built on 3D Gaussian Splatting. Its core idea: every Gaussian primitive carries an explicit **confidence** value, computed analytically from optimization dynamics at O(N) cost — and this single quantity closes the loop on *every* major decision the system makes.

| | Module | Equation (paper) | Role of confidence C<sub>i</sub> |
|---|---|---|---|
| **P** | Confidence estimation | Eq. (8) | Produced from gradient & opacity dynamics |
| **C1** | Keyframe selection | Eqs. (10)–(11) | Selects informative frames by expected information gain |
| **C2** | Adaptive optimization | Eqs. (12)–(13) | Modulates per-primitive learning rates |
| **C3** | Tracking | Eqs. (14)–(15) | Down-weights unreliable pixels in the pose objective |
| **C4** | Loop closure & PGO | Eqs. (24)–(25) | Sets residual-derived loop-edge covariances |

<div align="center">
<img src="assets/pipeline.png" width="92%" alt="System pipeline"/>

*Multi-stage, uncertainty-driven pipeline: pre-processing front-end, uncertainty-aware mapping back-end, and integrated coarse-to-fine loop closure.*
</div>

## Highlights

- **Uncertainty-Aware Gaussian Field (UGF)** — per-primitive confidence with formally analyzed properties (boundedness, monotonicity, boundary conditions; Proposition 1 in the paper) and a convergence guarantee for the confidence-modulated optimizer (Lemma 1).
- **Information-theoretic keyframe selection** — a map-state criterion (rendered confidence = expected information gain) instead of input-data heuristics; processes only ~10.5% of frames at higher quality than fixed-interval or co-visibility selection.
- **Confidence-modulated adaptive optimizer** — "cautiously aggressive" per-primitive learning rates: rapid correction where uncertain, fine-tuning where converged.
- **Coarse-to-fine loop closure on a unified sparse–dense map** — shadow Gaussians give robust PnP initialization; differentiable rendering gives high-precision refinement; final residuals parameterize the PGO edge covariance.
- **Real-time** — >30 FPS end-to-end with asynchronous mapping; runs onboard an RTX 3090 for robot deployments.

## Demos

A full demonstration of the running system is provided in [`demo/full_demo.mp4`](demo/full_demo.mp4) (13 MB), and can be watched directly on the [**project page**](https://anoymous-code.github.io/muSLAM/). It covers:

| Segment | Content |
|---|---|
| Real-time mapping | TUM-RGBD sequences mapped at native speed |
| On-robot runs | Wheeled platform (onboard RTX 3090): live camera feed + incrementally growing photorealistic Gaussian map |

<div align="center">
<img src="assets/confidence_module.png" width="55%" alt="Confidence estimation module"/>

*The differentiable confidence module maps optimization dynamics (gradient magnitude, opacity) to per-primitive confidence.*
</div>

## Results

**Tracking (ATE RMSE, cm) — TUM-RGBD.** Best average accuracy among all rendering-based and hybrid methods:

| Method | LC | RT | desk | desk2 | room | xyz | office | **Avg.** |
|---|---|---|---|---|---|---|---|---|
| SplaTAM | ✗ | ✗ | 3.35 | 6.54 | 11.13 | 1.24 | 5.16 | 5.48 |
| Photo-SLAM | ✗ | ✗ | 1.87 | 2.86 | 5.77 | 0.38 | **1.42** | 2.46 |
| 2DGS-SLAM | ✓ | ✗ | 1.84 | 2.76 | 5.98 | 1.16 | 1.97 | 2.74 |
| **μSLAM (ours)** | ✓ | ✓ | 1.67 | **2.45** | **5.35** | **0.30** | **1.42** | **2.24** |

**Robustness.** 100% success rate on 9 challenging TUM-RGBD sequences (aggressive motion, long-term navigation) at >30 FPS, where SplaTAM (67%) and GS-ICP-SLAM (89%) fail. See paper Tab. VII.

<div align="center">
<img src="assets/blur_comparison.jpg" width="85%" alt="Robustness to motion blur"/>

*Under aggressive motion at a loop closure: competing methods show ghosting from drift (a) and blur from unhandled motion artifacts (b); μSLAM (c) reconstructs fine details.*
</div>

<div align="center">
<img src="assets/tum_qualitative.png" width="92%" alt="Qualitative results on TUM-RGBD"/>
</div>

**Real-robot deployment.** Online mapping on a wheeled robot (onboard RTX 3090) across three environments, supporting downstream geometric (98.3%) and semantic (90.0%) navigation:

<div align="center">
<img src="assets/real_world.png" width="92%" alt="Real-world robot experiments"/>
</div>

## Configurations & Evaluation Protocol

To support scrutiny and reproduction of our experimental setup, this repository provides:

- [`configs/`](configs/) — the **complete hyperparameter set used in the paper**, matching Tab. III exactly (confidence gating β=100, γ=10; adaptive learning-rate bounds [0.1, 2.0]; per-frame/per-keyframe iteration counts; loop-closure thresholds). Settings are fixed across all experiments except the documented per-dataset keyframe scaling factor.
- [`docs/evaluation_protocol.md`](docs/evaluation_protocol.md) — the exact evaluation protocol: sequence lists for TUM-RGBD/ScanNet, ATE evaluation with `evo` (alignment settings included), rendering metrics (PSNR / SSIM / LPIPS-AlexNet) on novel views, and the success-rate criterion used for the robustness study.

## Code Release Plan

This anonymized repository accompanies a double-anonymous review and provides demonstration materials, configurations, and evaluation protocols. **The full source code of the four core modules** — confidence estimation (Eq. 8), information-theoretic keyframe selection (Eqs. 9–11), the confidence-modulated optimizer (Eqs. 12–13), and coarse-to-fine loop closure (Eqs. 22–25) — **will be released publicly in this repository upon publication of the article**, together with de-anonymized documentation.

## License

Released under the MIT License (see [LICENSE](LICENSE)).

## Citation

Citation information will be added upon publication (double-anonymous review in progress).
