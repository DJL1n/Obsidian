# Feed-Forward Submap

## Definition

在 RGB SLAM 中，不退回 pairwise matching，而是让 feed-forward reconstruction model（VGGT）一次处理一个中等大小的局部窗口（约 60 帧），直接输出 camera、depth、point cloud、confidence。然后将子图通过 factor graph 全局对齐。VGGT-SLAM 是代表实现。

## Why it matters

Pairwise（MASt3R）局部性强，multi-frame feedforward 能利用全局 attention 得到更完整的局部几何。但受显存限制需分 submap，且子图之间有 projective ambiguity。

## Related notes

- [[geometry-priors/grounded/VGGT-SLAM]]
- [[Projective-Ambiguity-Awareness]]
- [[SL4-Submap-Alignment]]
