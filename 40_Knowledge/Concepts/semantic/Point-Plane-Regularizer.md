# Point-Plane Regularizer

## Definition

在 Bundle Adjustment 中，对属于 planar cluster 的每个 3D point 加入 unary point-plane constraint，作为 soft regularizer：

min Σ reprojection_error + Σ distance(X_j, π_k)

其中 π_k 是从 cluster C_k 拟合的平面，distance 用 Huber loss 且根据 plane uncertainty 加权。

## Why it matters

普通 BA 只约束"点投影到图像上要对"。Point-plane regularizer 额外约束"属于 floor/table/book 的点在 3D 中要接近其对应平面"，让地图结构更物理合理。

## Key distinctions

| | Hard projection | Soft regularizer (S3LAM) |
|---|---|---|
| 约束方式 | 把点直接投影到平面 | 加 residual，不强制精确在平面 |
| 风险 | 错误平面拉坏地图 | Huber + uncertainty 控制 |
| Outlier 处理 | 无 | chi-squared 95th percentile 拒绝 |
| 平面优化 | 可能 joint optimize | 不作为变量优化 |

## 设计细节

- 用 g2o 实现：classical BA graph + unary constraint per planar point
- Huber loss 减轻 outlier 影响
- 根据 plane fitting uncertainty 加权
- Plane 参数不作为 active variable 优化（fitted prior 而非 landmark）
- 超过 chi-squared 95th percentile 标记为 outlier

## 对 SkelGS-SLAM 的借鉴

不要：
- plane fit 后直接改 depth/pose/Gaussian

应该：
- plane prior 作为 candidate factor / confidence factor
- 只影响 admission / weighting / certification（与 no-writeback 一致）

## Related notes

- [[mapping/structured/S3LAM]]
- [[Semantic-Cluster-Structure-Prior]]
- [[Structured-Anchor-Group]]
