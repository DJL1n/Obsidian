# Semantic Backend Regularizer

## Goal

在 GS backend 中，利用语义一致性作为 regularizer：同一 mature anchor / same support region 的 semantic feature 应一致；不同 object / dynamic-risk region 的 semantic feature 应分离。只作为 backend regularizer，不反向影响 pose/depth/anchor authority。

## Design

- 同一 object/region anchor: semantic feature 拉近
- 不同 object/dynamic-risk anchor: semantic feature 推远
- Cross-view semantic consistency: rendered semantic feature map 跨视角一致
- 语义不作 pose/depth/anchor certification 证据
- 只用于 GS backend appearance/object-level 优化

## Related notes

- [[OpenMonoGS-SLAM]]
- [[Compact-Semantic-Memory]]
- [[Multi-Scale-Anchor-Admission]]
