# Pointmap Regression

## Definition

直接回归每个像素在 3D 空间中的完整坐标 (x, y, z)，而不是先估 depth（需要 K 反投影）再三角化。Pointmap 是 camera-agnostic 3D coordinate field，统一 monocular 和 binocular reconstruction。

## Why it matters

传统 depth map 需要相机内参才能反投影成 3D。Pointmap 直接输出 3D 坐标，无需已知 K，使 uncalibrated dense reconstruction 成为可能。且 same-coordinate pointmaps 天然支持 3D-grounded matching。

## Key distinctions

| | Depth map | Pointmap |
|---|---|---|
| 输出 | z (需 K 反投影) | (x, y, z) 直接 |
| 需要相机内参 | 是 | 否 |
| 匹配方式 | epipolar / depth-based | 3D coordinate proximity |
| 统一 mono/stereo | 否 | 是 |

## Related notes

- [[geometry-priors/feed-forward/DUSt3R]]
- [[Uncalibrated-Dense-Reconstruction]]
- [[Pointmap-to-Geometry]]
