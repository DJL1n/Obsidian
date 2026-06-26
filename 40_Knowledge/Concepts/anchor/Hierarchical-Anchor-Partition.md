# Hierarchical Anchor Partition

## Definition

将 anchors 按 voxel size 分成多个层级（L0 coarsest → L2 finest），coarser anchors 先编码，finer anchors 以 coarser 为 context 后编码。Bottom-up partition：大 voxel 量化 → 选 representative → 逐级。

## Why it matters

所有 anchors 在同一 level 编码无法利用层级间依赖。分层后 coarse anchors 先解码，提供 finer anchors 的上下文先验，使 autoregressive context model 可运行。

## Related notes

- [[mapping-reconstruction/ContextGS]]
- [[Anchor-Level-Context-Model]]
- [[Anchor-Compression-for-SLAM]]
