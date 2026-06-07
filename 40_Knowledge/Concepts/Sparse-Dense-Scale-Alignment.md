# Sparse-Dense Scale Alignment

## Definition

在 monocular GS-SLAM 中，通过稀疏点云统计校正密集深度，使 sparse VO scale、MVS prior depth scale 和 Gaussian map scale 保持一致的三方闭环。MGS-SLAM 的 SDAR 是代表实现。

## Why it matters

Monocular SLAM 中 tracking scale、depth scale、GS map scale 天然不一致。如果不处理，MVS depth 监督错，GS map scale 漂，frontend tracking 和 backend mapping 互相拉扯。

## Key distinctions

| | None | Simple scale fix | SDAR-style closure |
|---|---|---|---|
| Scale handling | 无 | 后处理对齐 | 闭环反馈 |
| Sparse ↔ dense | 无连接 | 单向 | 双向 |
| GS ↔ frontend | 无 | 无 | rendered depth feedback |

## Related notes

- [[MGS-SLAM]]
- [[SDAR-Mechanism]]
- [[Predicted-Depth-GS-Supervision]]
