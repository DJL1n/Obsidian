# Loss-Weighted Refinement

## Goal

在 GS backend 优化中，按 keyframe 的 rendering loss 加权采样，高 loss 帧更高概率被选中优化，低 loss 帧降低频率。FlashSLAM 的实验：loss-weighted > worst-first > random。

## Why it matters

优化预算应按残差分配，而不是平均分配。高残差帧说明当前 map 在该视角下欠拟合，应优先修复；均匀采样会浪费预算在已经拟合好的帧上。

## Related notes

- [[FlashSLAM]]
- [[Explicit-Tracking-vs-Render-Tracking]]
- [[Alignment-Check-Before-Birth]]
