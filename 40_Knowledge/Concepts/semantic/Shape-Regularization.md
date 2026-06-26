# Shape Regularization

## Definition

约束 2D Gaussian disk 整个表面（不止 center）对齐 SDF zero-level set：在 disk surface 采样点 → query SDF → 对齐。监督 center + rotation + scale + opacity + shape。

## Why it matters

只约束 Gaussian center 拉到 surface 不够，因为 disk 可能倾斜或穿透 surface。Shape regularization 让整个 splat 贴合 surface，保留更多表面细节，减少漂浮伪影。GS-SDF ablation 显示 shape reg 优于 center reg。

## Key distinctions

| | Center reg | Shape reg |
|---|---|---|
| 约束点 | 1 (center) | disk surface samples |
| 影响参数 | position | position + rotation + scale + opacity |
| 细节保留 | 一般 | 更好 |
| 抗漂浮 | 中等 | 更强 |

## Related notes

- [[mapping/sdf-based/GS-SDF]]
- [[SDF-Geometry-Teacher]]
- [[NSDF-to-GS-Initialization]]
