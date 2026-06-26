# Submap Gaussian Organization

## Definition

将 3D Gaussian map 拆成多个独立的 sub-maps（每个对应一段局部区域），每次只优化 active sub-map。新 sub-map 在相机运动超过阈值时创建，旧 sub-map 被冻结。

## Why it matters

Vanilla 3DGS 全局优化不适用于在线 SLAM：Gaussian 数量随场景增长，全部放入 GPU 不可行。Sub-map 将优化限制在当前活跃区域，使计算不随全局场景大小增长。

## Key distinctions

| | Global map | Sub-map (Gaussian-SLAM) |
|---|---|---|
| 优化范围 | 所有 Gaussians | active sub-map only |
| 可扩展性 | 差 | 好 |
| Loop closure | 需全局 | sub-map 可独立处理 |
| 资源需求 | 随场景增长 | bounded |

## Related notes

- [[gs-slam/monocular/Gaussian-SLAM]]
- [[GS-Birth-Gate]]
