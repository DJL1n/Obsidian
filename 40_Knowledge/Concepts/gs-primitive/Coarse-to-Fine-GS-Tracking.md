# Coarse-to-Fine GS Tracking

## Definition

在 GS-SLAM 中，通过 coarse-to-fine 策略优化 camera pose：先低分辨率/稀疏采样粗略估计 pose，再选择 reliable Gaussians 做全分辨率精调。

## Why it matters

GS map 中包含 artifact-producing / noisy Gaussians。如果直接用所有 Gaussians 优化 pose，坏 Gaussian 会拉偏 pose。Coarse stage 低分辨率避开细节噪声；Fine stage 只选可靠的、已充分优化的区域参与 pose optimization。

## Procedure

### Coarse stage
- Uniform sampled pixels
- Low-cost render
- Coarse pose estimate (减少 detailed artifacts 影响)

### Fine stage
- Coarse pose + depth observation
- Select reliable 3D Gaussians (previously observed areas)
- Full-resolution render
- Refine pose (忽略 artifact-producing Gaussians)

## Core insight
GS map feedback 必须被 gating；不能让坏 Gaussian / 未建图区域污染 pose。

## Related notes

- [[3dgs-slam/GS-SLAM]]
- [[Adaptive-Gaussian-Expansion]]
- [[Silhouette-Gated-Tracking]]
