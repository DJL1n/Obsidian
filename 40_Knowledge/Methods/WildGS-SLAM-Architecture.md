# WildGS-SLAM Architecture

## Goal

Monocular RGB dynamic-scene GS-SLAM: DROID-style tracking + DINOv2 uncertainty MLP + Metric3D depth + uncertainty-aware DBA + uncertainty-weighted GS static mapping。

## Components

### Uncertainty module
DINOv2 feature → shallow MLP → per-pixel uncertainty。Online trained, detached from GS gradients。

### Tracking
DROID-based DBA + uncertainty weighting + Metric3D disparity regularization。

### Mapping
Metric3D proxy depth → Gaussian expansion。Uncertainty-weighted RGB/depth loss + isotropic reg。

### Final refinement
Global BA (remove disparity reg) + fix poses → optimize uncertainty + GS map + non-keyframe re-rendering。

## Related notes

- [[WildGS-SLAM]]
- [[Uncertainty-Dynamic-Filtering]]
- [[Certified-Dynamic-Gate]]
