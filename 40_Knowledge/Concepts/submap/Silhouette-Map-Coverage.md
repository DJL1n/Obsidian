# Silhouette Map Coverage

## Definition

在 GS-SLAM 中，通过 rendered silhouette / alpha map 表示当前 map 的空间覆盖范围。Silhouette 高表示像素已被已有 Gaussian map 覆盖，低表示未建图或建图不足。

## Why it matters

判断 map 是否覆盖某个区域是 GS-SLAM 中最关键的机制之一。Silhouette 捕捉的是 map 的 epistemic uncertainty：哪些区域已有可靠 geometry，哪些还没有。这直接服务于 tracking gate（只在可信区域算 loss）和 densification（在未覆盖区域加 Gaussian）。

## Key distinctions

| | Silhouette high | Silhouette low |
|---|---|---|
| Map coverage | 已覆盖 | 未覆盖/不足 |
| Tracking | 可用 | 不可用 |
| Densification | 无需 | 需要 |
| 含义 | 低 epistemic uncertainty | 高 epistemic uncertainty |

## Related notes

- [[3dgs-slam/SplaTAM]]
- [[Silhouette-Gated-Tracking]]
- [[RGB-D-Densification-Mask]]
