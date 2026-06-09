# Predicted Depth GS Supervision

## Goal

在 monocular GS-SLAM 中用 predicted dense depth 监督 Gaussian map，但通过稀疏校正和 smoothness regularization 避免错误 depth 污染 GS geometry。

## Background

MGS-SLAM 的 MVS prior depth 不完全准确。直接用它监督 Gaussian map 会把错误深度写入地图。解决方案：SDAR 校正 + depth smooth loss + isotropic loss。

## Procedure

### SDAR correction
Sparse point cloud statistics → correct MVS depth → optimized prior depth

### Depth smooth regularization
约束 rendered depth map 邻域平滑性，降低错误 prior depth 对 Gaussian 几何的逐像素影响。

### Four-part loss
L = photo (RGB L1) + depth (rendered vs corrected prior) + smooth (neighborhood) + iso (anti-elongation)

## Core insight
Predicted depth / MASt3R depth / MVS depth / any estimated depth → 不能直接作为 GS birth truth。必须经过：smoothness + multi-view consistency + normal + free-space + scale + temporal stability。

## Related notes

- [[MGS-SLAM]]
- [[SDAR-Mechanism]]
- [[Sparse-Dense-Scale-Alignment]]
