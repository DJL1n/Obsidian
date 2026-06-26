# GS Tracking Representation

## Definition

使用 3D Gaussian Splatting map 作为 camera tracking 的直接表示，通过 differentiable rasterization 从 rendered image/depth 与 observed image/depth 的 residual 中优化 camera pose。

## Why it matters

MonoGS 证明 3DGS map 对 camera pose optimization 的 convergence basin 比 hash-grid SDF 和 MLP SDF 明显更大。这意味着 GS map 即使不是 perfect geometry，也能在较大误差范围内拉回 camera pose，降低 tracking 对前端的依赖。

## Key distinctions

| | Neural SDF map | GS map |
|---|---|---|
| Tracking 收敛盆 | 小 | 大 |
| 渲染速度 | 慢 (ray marching) | 快 (rasterization) |
| 可微性 | 可微但复杂 | 可微且高效 |
| 几何显式性 | 隐式 SDF | 显式 GS primitives |

## Related notes

- [[3dgs-slam/MonoGS]]
- [[Gaussian-Covisibility]]
- [[Silhouette-Map-Coverage]]
