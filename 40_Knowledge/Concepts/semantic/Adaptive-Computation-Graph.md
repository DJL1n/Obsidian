# Adaptive Computation Graph

## Definition

在推理过程中根据输入难度动态调整网络计算量：easy pairs 提前退出（adaptive depth），不可匹配点从后续层中删除（adaptive width）。

## Why it matters

传统模型对所有输入使用固定计算图，但实际 SLAM/SfM 中很多图像对很简单（连续帧：大 overlap、小视角变化）。对它们跑满完整深度是浪费。Adaptive computation 让模型在 easy pairs 上快 1.86×，总 runtime 减少约 33%。

## Key distinctions

| | Fixed graph | Adaptive graph |
|---|---|---|
| Depth | 所有输入相同 | easy 早退，hard 深跑 |
| Width | 所有点参与全程 | 不可匹配点 prune |
| 计算效率 | 低 | 高（易快难慢） |
| 训练要求 | 简单 | 需 deep supervision + 置信度分类器 |

## Design

- 每层后预测 assignment 和 confidence
- 如果 confidence 足够 → stop
- 如果某些点被高置信度判为 unmatchable → prune
- 后续层只处理剩余的可匹配点

## Related notes

- [[matching-representation/LightGlue]]
- [[Sparse-Learned-Matcher]]
