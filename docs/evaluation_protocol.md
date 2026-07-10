# Evaluation Protocol

This document specifies the exact evaluation protocol used in the paper, to support scrutiny and independent reproduction of the reported comparisons.

## Datasets and Sequences

**TUM-RGBD** (tracking & rendering, Tabs. IV-A/V-A): `fr1/desk`, `fr1/desk2`, `fr1/room`, `fr2/xyz`, `fr3/long_office_household`.

**TUM-RGBD robustness study** (Tab. VII), 9 sequences in three motion-profile categories:
- High view overlap: `fr1/xyz`, `fr1/rpy`, `fr2/rpy`
- Aggressive motion: `fr1/360`, `fr1/floor`, `fr2/hemisphere`
- Long-term navigation: `fr2/large_no_loop`, `fr2/large_with_loop`, `fr2/pioneer_slam`

**ScanNet** (Tabs. IV-B/V-B): scenes `0000_00`, `0059_00`, `0106_00`, `0181_00`, `0207_00`.

## Tracking Accuracy

- Metric: **ATE RMSE [cm]** on the estimated camera trajectory.
- Tooling: [`evo`](https://github.com/MichaelGrupp/evo) (`evo_ape`), SE(3) Umeyama alignment, no scale correction (RGB-D metric scale).
- ScanNet caveat: ground-truth trajectories are themselves BundleFusion estimates; sub-centimeter differences are not meaningful.

## Rendering Quality

- Metrics: **PSNR**, **SSIM**, **LPIPS** (AlexNet backbone) on novel views held out from mapping.
- Rendering uses the final online map (no offline post-refinement).

## Robustness & Efficiency

- **Success rate**: a run is successful iff the sequence completes without tracking failure; failures marked ✗ are excluded from that method's quality averages (averages computed over completed runs only).
- **FPS**: end-to-end throughput of the main tracking loop, including pre-filtering; mapping runs asynchronously (see the runtime breakdown in paper Tab. VI).

## Hyperparameters

All hyperparameters are provided in [`../configs/tum.yaml`](../configs/tum.yaml) and match paper Tab. III exactly. They are fixed across all experiments, with one documented exception: the keyframe-selection scaling factor `k_select` is set per dataset to account for camera frame rates and typical motion speeds.
