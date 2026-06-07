# Pointmap to Geometry

## Goal

从 DUSt3R 输出的 shared-coordinate pointmaps 中 恢复传统几何量：depth, pixel matches, relative pose, camera intrinsics。

## Procedure

### Depth
Pointmap 在 camera frame 下 → z component = D(u, v)

### Pixel matches
X1(p) ≈ X2(q) in shared coordinate → p ↔ q

### Relative pose
3D-3D alignment / PnP from corresponding pointmap points

### Camera intrinsics
从 pointmap + image coordinate 关系反推近似 camera model / focal length

## Significance

传统几何视觉任务变成 pointmap 输出上的后处理。这就是 DUSt3R 统一多个 geometric 3D vision tasks 的方式。

## Related notes

- [[DUSt3R]]
- [[Pointmap-Regression]]
- [[Uncalibrated-Dense-Reconstruction]]
