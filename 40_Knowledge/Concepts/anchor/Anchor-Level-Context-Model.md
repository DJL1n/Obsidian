# Anchor-Level Context Model

## Definition

在 anchor-based 3DGS compression 中，利用已解码的 coarser-level anchors 作为上下文，预测尚未编码的 finer-level anchors 的 feature 概率分布，从而实现更高效的 entropy coding。本质是自回归上下文建模。

## Why it matters

相邻 anchors 的 feature/scale/local Gaussian 属性有强空间相关性。独立编码浪费 bits。Context model 利用这种相关性，大幅降低 anchor feature bitstream 体积。ContextGS 报告相比 Scaffold-GS 约 15× 压缩。

## Key distinctions

| | Independent coding | Context model |
|---|---|---|
| 每个 anchor | 单独编码 | 由 coarse anchors 预测 |
| 空间依赖 | 未利用 | 充分利用 |
| Bitrate | 高 | 低 |
| 编码复杂度 | 低 | 中（需解压 context） |

## Related notes

- [[mapping-reconstruction/ContextGS]]
- [[Hierarchical-Anchor-Partition]]
- [[Anchor-Compression-for-SLAM]]
