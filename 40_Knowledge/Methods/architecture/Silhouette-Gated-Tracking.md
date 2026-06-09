# Silhouette-Gated Tracking

## Goal

在 GS-SLAM 中通过 rendered silhouette mask 控制哪些 pixels 参与 tracking loss，只信任 map 已覆盖且优化充分的区域。

## Background

SplaTAM 证明 silhouette mask 是 GS-SLAM tracking 的核心安全门。不用 mask 时 ATE=115.80 cm，用 mask 时 ATE=0.27 cm。

## Procedure

1. Render current Gaussian map → color, depth, silhouette
2. Compute silhouette mask: S(p) > 0.99
3. Compute tracking loss only on masked pixels:
   L_tracking = L1(depth) + 0.5 * L1(color)  [仅 silhouette>0.99]
4. Optimize camera pose (fixed Gaussian params)
5. Forward velocity propagation for pose init

## Key principles

- 未建图/新区域/不可信区域 → 不参与 tracking loss
- Silhouette 捕捉 map 的 epistemic uncertainty
- 阈值 0.99 比 0.5 明显更好（SplaTAM ablation）
- 避免错误/不完整 map 污染 pose

## 对 SkelGS-SLAM 的借鉴

GS render feedback 不能全图使用，必须被 alpha/visibility/packet confidence mask 控制。如果未来允许 GS feedback，必须通过 silhouette gate，且只能作为 weak evidence。

## Related notes

- [[SplaTAM]]
- [[Silhouette-Map-Coverage]]
- [[RGB-D-Densification-Mask]]
