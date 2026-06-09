# Adaptive Gaussian Expansion

## Definition

在线 RGB-D GS-SLAM 中通过 adaptive adding（补新区域）和 deletion（删漂浮 Gaussians）来控制 Gaussian map 增长，使 3DGS 能重建完整场景而非只合成静态物体。

## Procedure

### Adding
1. Render RGB-D + cumulative opacity from existing Gaussians
2. Unreliable pixels: opacity too low OR rendered depth far from observed depth
3. Back-project these pixels → new Gaussians

### Deletion
1. Visible Gaussians → project to current depth image
2. Compare Gaussian z-depth with observed depth
3. If not near surface → reduce opacity (delete/weaken)

## Why it matters

离线 3DGS 有已知 pose + dense view coverage。在线 SLAM 中视角逐步增长，必须持续添加新区域 Gaussians，同时删除优化中产生的 floating/noisy Gaussians。GS-SLAM 证明了 adaptive expansion 的基础框架。

## Related notes

- [[GS-SLAM]]
- [[Coarse-to-Fine-GS-Tracking]]
- [[GS-Birth-Gate]]
