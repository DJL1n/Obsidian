# Isotropic GS for SLAM

## Definition

在 online GS-SLAM 中使用各向同性 Gaussians（单一 radius 参数替代 anisotropic covariance + rotation + scale），并使用 view-independent RGB color 替代 spherical harmonics。SplaTAM 每个 Gaussian 仅 8 参数。

## Why it matters

Vanilla 3DGS 的 anisotropic covariance + SH color 参数多，优化需要大量 iterations。SLAM 中每帧都要做 tracking + mapping，不能像离线 3DGS 那样长时间优化。Isotropic 简化让在线优化更快、更稳定。

## Key distinctions

| | Vanilla 3DGS | Isotropic GS-SLAM |
|---|---|---|
| Covariance | anisotropic (R S S^T R^T) | isotropic (single r) |
| Color | SH coefficients | view-independent RGB |
| 参数/GS | ~60+ | 8 |
| 适合在线 | 否 | 是 |
| 渲染质量 | 更高 | 略低但够用 |

## Related notes

- [[gs-slam/rgbd/SplaTAM]]
- [[Silhouette-Map-Coverage]]
