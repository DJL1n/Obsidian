# Semantic Cluster Structure Prior

## Definition

将 SLAM map points 按语义类别和实例 ID 聚成 clusters，对可近似为平面等 primitives 的 cluster 施加结构先验约束（如 point-plane regularizer），使 map 从 unstructured point cloud 变为 semantic clustered structured map。

## Why it matters

传统 SLAM 的 map points 缺乏语义含义，BA 只知道 reprojection error。Semantic cluster 提供两点价值：
1. 多个 points 一起比单点更可靠（统计支撑）
2. 语义类别暗示了几何结构类型（如 floor → planar）

## Key distinctions

| | Unstructured points | Semantic cluster |
|---|---|---|
| 分组 | 无 | 按语义 class + instance |
| 单点可靠性 | 低 | 高（统计支撑） |
| 结构假设 | 无 | 语义暗示几何 primitive |
| 约束方式 | 无 | soft regularizer |

## Common failure modes

- Segmentation 错误 → cluster 错误 → 结构先验污染
- 非平面 class（chair, plant）强行套 plane 会出问题
- Instance ID 跨帧不一致需要专门 tracking/merge 策略

## Related notes

- [[mapping/structured/S3LAM]]
- [[Point-Plane-Regularizer]]
- [[Structured-Anchor-Group]]
