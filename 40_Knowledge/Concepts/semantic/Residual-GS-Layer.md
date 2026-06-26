# Residual GS Layer

## Definition

一种 GS 设计范式：Gaussian 不再是全场景主地图，而是作为 certified geometry 上的 appearance residual layer。SDF/geometry 承担主体几何和基础颜色；Gaussian 只负责高频外观残差修正。GPS-SLAM 是代表实现。

## Why it matters

全场景 Gaussian map 优化重、几何不稳、对错误 depth 敏感。Residual GS 大幅减少 Gaussian 数量和优化成本，同时保留高保真 appearance。GPS-SLAM 150+ FPS 的核心原因之一。

## Key distinctions

| | Full GS map | Residual GS |
|---|---|---|
| 几何主体 | Gaussian | SDF / certified geometry |
| Gaussian 职责 | geometry + appearance | appearance residual only |
| Gaussian 数量 | 多 | 少（~50% fewer） |
| 对 geometry 依赖 | 弱（需自建） | 强（依赖外部几何场） |
| 速度 | 慢（<20 FPS） | 快（150+ FPS） |

## Related notes

- [[mapping-reconstruction/GPS-SLAM]]
- [[Geometry-First-GS-Pipeline]]
- [[Certified-GS-Residual]]
