# Dense-Sparse Memory

## Definition

一种双层 spatial memory 架构：dense working memory 保留最近帧的高分辨率上下文，sparse long-term memory 通过 attention-importance sparsification 保留长期中被最频繁查询的 tokens。

## Why it matters

单层 dense memory 随序列增长显存爆炸。Dense-sparse 拆分：working memory 提供近期高精度上下文，long-term memory 提供长期全局稀疏上下文，兼顾质量与可扩展性。

## Key design

- Working memory: recent 5 frames, dense, 插入前相似性检查去重
- Long-term memory: sparse, 累计 attention weight, top-k 保留（~4000 tokens）
- Drain: working memory 满后最老的 drain 到 long-term
- Attention clipping: 小 weight tokens 可能含 outlier → hard clip + renormalize

## 对 Anchor skeleton 的对应

- working memory → recent active anchors / packet cache
- long-term memory → mature certified anchors
- attention clipping → outlier/hard-reject gate
- sparsification → only keep geometry-stable, high-support anchors

## Related notes

- [[geometry-priors/feed-forward/Spann3R]]
- [[Spatial-Memory-Reconstruction]]
- [[Spatial-Memory-for-Anchor]]
