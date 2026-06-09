# Sparse Learned Matcher

## Definition

一种 learned sparse local feature matcher，使用 Transformer attention 做跨图像上下文聚合，但不使用 Sinkhorn optimal transport，而是用 similarity + matchability 的组合来做 assignment。

## Why it matters

传统 NN matching + ratio test 在大 viewpoint/illumination 变化下容易错。SuperGlue 用 Sinkhorn 解决了这个问题但太重。Sparse learned matcher（如 LightGlue）在保持鲁棒性的同时大幅降低计算成本，且 matchability 显式建模允许模型说"这个点不应匹配"。

## Key distinctions

| | NN + ratio test | SuperGlue | LightGlue |
|---|---|---|---|
| Context | 无 | Transformer attention | Transformer attention |
| Assignment | L2 distance | Sinkhorn OT | similarity × matchability |
| Matchability | 隐式 | dustbin | 显式预测 |
| 不可匹配点 | 强迫最近邻 | Sinkhorn 分配 | 显式拒绝 |
| 速度 | 快 | 慢 | 快 |
| Early exit | 无 | 无 | adaptive depth + width |

## Common failure modes

- 低纹理区域无 keypoints 时无法匹配（dense matcher 可弥补）
- 动态物体稳定纹理仍可能输出错误但一致的匹配
- 受 detector/descriptor 质量上限限制

## Related notes

- [[LightGlue]]
- [[Adaptive-Computation-Graph]]
- [[Matchability-Assignment]]
