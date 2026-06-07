# Correlation Volume (4D)

## Definition

两帧 dense feature map 之间所有像素对的点积相似度表：

C_ij[u1, v1, u2, v2] = feature_i[u1, v1] · feature_j[u2, v2]

后两个维度做 average pooling 构成 4-level correlation pyramid。

## Why it matters

传统方法先检测匹配点再估计几何。Correlation volume 把"哪里可能是匹配"的信息保留为 dense visual similarity field，让 recurrent update operator 可以在优化过程中反复查询，而不是一次性固定匹配。

## Key distinctions

| | Feature matching | Correlation volume |
|---|---|---|
| 匹配决策 | 一次性 hard assignment | 保留所有候选的相似度 |
| 查询方式 | 固定匹配点 | 在预测位置附近 lookup |
| 更新能力 | 无法修正 | 每轮迭代可重新查询 |
| 计算成本 | O(N) | O(N²) 但可 pooling |

## 在 DROID-SLAM 中的用法

1. 对 frame graph 每条边 (i, j) 构建 correlation volume
2. 当前几何投影预测 correspondence p_ij
3. 在 pyramid 上围绕 p_ij 做 lookup
4. lookup 结果输入 ConvGRU
5. ConvGRU 预测 revision flow r_ij
6. 下一轮用更新后的 pose/depth → 新的 p_ij → 新的 lookup

## 对 anchor 方向的价值

Correlation volume lookup 提供密集的 visual evidence，不需要 sparse feature detection，因此：
- 无纹理区域仍有信号（靠 learned feature similarity）
- Covisible frame pairs 可自动建立密集约束
- Lookup 位置和 similarity score 可做匹配质量 proxy

## Common failure modes

- 存储和计算成本高（4D volume → 需 memory-efficient 实现）
- Large baseline 时 similarity 可能退化
- 光照剧烈变化时 feature similarity reliability 下降

## Related notes

- [[DROID-SLAM]]
- [[Recurrent-Correction-Update]]
- [[Dense-Bundle-Adjustment]]
