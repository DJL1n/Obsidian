# Probabilistic Anchor Update

## Definition

在 GS-SLAM 中，通过 Bayesian update（prior + observation + likelihood → posterior probability）管理 anchor 状态，替代手工阈值（alpha/depth error/gradient）。Anchor probability 表示 motion degree / dynamic / occupancy 置信。UP-SLAM 是代表实现。

## Why it matters

手工阈值在动态场景中不可靠：太松→冗余 anchors + GPU memory 涨；太紧→静态不完整。概率更新自动抑制动态 anchors，显著减少模型体积（UP-SLAM: 22.92 MB → 7.01 MB），同时保持或提升 quality。

## Related notes

- [[gs-slam/dynamic/UP-SLAM]]
- [[Multi-Modal-Uncertainty]]
- [[Certified-Probabilistic-Anchor]]
