# Dynamic-Static Composite Mapping

## Goal

在动态场景中不简单地删除动态物体，而是将 static background 和 dynamic objects 分别构建 Gaussian 表示。ADD-SLAM 的代表实现。

## Procedure

1. 初始化为完全静态 Gaussian map
2. Dynamic detection → 分离 dynamic Gaussians from static map
3. 静态 map：移除动态 Gaussians + 填补暴露背景 holes
4. 动态 map：per-object temporal Gaussian sequence（逐时刻初始化+优化）

## Related notes

- [[ADD-SLAM]]
- [[Consistency-Dynamic-Detection]]
- [[Occlusion-vs-Exposure-Cue]]
