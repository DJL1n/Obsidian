# Patch Graph VO

## Definition

一种用于 visual odometry 的 sparse graph 表示：bipartite graph 连接 patches 和 video frames。每个 patch 与其 source frame 前后一定时间距离内的 frames 建边，patch 在所有 connected frames 中的 reprojection 构成该 patch 的 trajectory。

## Why it matters

传统 sparse keypoint VO 只做 pairwise matching，无 temporal graph 结构。Dense VO（DROID）提供完整 temporal 优化但太重。Patch graph 用少量 patches + 局部 temporal edges 实现高效、稳定、可追踪的 VO。

## Key distinctions

| | Pairwise matching | Dense graph (DROID) | Patch graph (DPVO) |
|---|---|---|---|
| 节点 | keypoints | dense pixels | sparse patches + frames |
| 边 | 单对 | 全连接 | 局部 temporal + covisibility |
| Temporal trace | 无 | dense flow | patch trajectory |
| 计算 | 轻 | 重 | 中-轻 |

## 对 Anchor 的价值

DPVO patch graph 天然提供 anchor-like 信号：patch lifetime, edge confidence, trajectory revision, BA residual, depth update, visibility, motion consistency。

## Related notes

- [[DPVO]]
- [[Patch-Lifecycle]]
- [[DPVO-Temporal-Frontend]]
