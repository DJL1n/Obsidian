# SDF Geometry Teacher

## Definition

用外部连续几何场（如 neural SDF）作为 Gaussian splats 的几何监督源，替代/补充 RGB render loss 和 render-derived depth/normal 正则。GS-SDF 用 LiDAR-NSDF 实现。

## Why it matters

Render-derived constraints 在复杂场景中不够（GS-SDF ablation 证明会产生 false structure）。外部连续几何场给 Gaussian 提供 SDF zero-level、normal、curvature 等明确几何信号，不只靠 photometric loss 或 rendered-normal consistency。

## Key distinctions

| | Render-derived | SDF teacher |
|---|---|---|
| 几何信号 | rendered depth/normal | SDF zero-level + gradient |
| 约束范围 | per-pixel | per-surface-disk |
| 可靠性 | 复杂场景 may fail | 外部几何源更稳 |
| 需要额外输入 | 否 | LiDAR / 可信几何场 |

## Related notes

- [[GS-SDF]]
- [[Shape-Regularization]]
- [[Certified-Geometry-Field]]
