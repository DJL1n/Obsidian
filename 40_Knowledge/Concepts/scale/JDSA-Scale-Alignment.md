# JDSA Scale Alignment

## Definition

在 monocular GS-SLAM 中，通过 2D spatially varying scale grid 对齐 depth prior 与 BA-estimated depth，修正 monocular depth prior 的空间尺度扭曲。HI-SLAM2 的 JDSA 是代表实现。

## Why it matters

Monocular depth prior 的问题不是简单全局 scale 不准，而是不同区域有不同 scale distortion。Single scale alignment 不够。JDSA 用 2D grid + bilinear interpolation 实现 per-pixel level scale correction，且与 BA 交替优化避免 scale drift。

## Related notes

- [[gs-slam/monocular/HI-SLAM2]]
- [[Gaussian-Map-Deformation]]
- [[Certified-JDSA]]
