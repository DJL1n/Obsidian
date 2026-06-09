# RGB-D Densification Mask

## Goal

在 GS-SLAM 中通过 rendered silhouette 和 depth discrepancy 判断哪些区域需要新增 Gaussians，替代 vanilla 3DGS 的 gradient-based densification。

## Background

SplaTAM 的 densification 不依赖 gradient split/clone，而是基于 RGB-D 观测：silhouette low → 未覆盖；depth 前方有新几何 → 漏建了前景。

## Procedure

### Densification mask
需要 densify 的 pixels:
1. S(p) < 0.5 (silhouette low → map 没覆盖)
2. D_GT(p) < D_render(p) 且 depth error > λ * median depth error (λ=50) (GT depth 在 rendered depth 前方 → 漏了前景)

### 对 mask 中的每个 pixel
- 按第一帧初始化流程添加 Gaussian
- 使用 GT depth 初始化 Gaussian center

## Contrast

| | Vanilla 3DGS | SplaTAM | Gaussian-SLAM |
|---|---|---|---|
| 信号 | gradient high | silhouette + depth | alpha + NN sparsity |
| 几何依赖 | 无 | RGB-D depth | RGB-D depth |
| 控制力 | 弱 | mask 阈值 | NN radius + alpha |

## 对 SkelGS-SLAM 的借鉴

无真实 RGB-D depth，可迁移为：
candidate Gaussian birth if:
- rendered alpha/silhouette low
- certified geometry packet says surface exists
- DPVO/DROID temporal evidence supports
- depth-normal predictor agrees
- no free-space contradiction
- nearby anchors/Gaussians insufficient

## Related notes

- [[SplaTAM]]
- [[Silhouette-Map-Coverage]]
- [[Silhouette-Gated-Tracking]]
