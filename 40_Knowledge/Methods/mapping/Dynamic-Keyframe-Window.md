# Dynamic Keyframe Window

## Goal

在在线 mapping 中，避免 fixed window 引起的新区域过拟合和旧区域遗忘。通过每次 iteration 动态采样 local + global keyframes 构建优化 window。

## Background

OG-Mapping 中：新 keyframe 通常表示新区域 → 只用该 keyframe 导致 forgetting/overfitting → fixed window 难以平衡。因此每次 iteration 清空 window（除新 keyframe），从 local set + global set 无放回采样。

## Procedure

1. 保留新 keyframe K_new
2. Local keyframes: 与 K_new overlap 较高
3. Global keyframes: 历史中 overlap 较低 / 分散
4. 每次 iteration: K_new + random local + random global

## 对 SkelGS-SLAM 的借鉴

可改造为 Dynamic Certified Packet Window：
- keep current/new certified packet
- sample local high-overlap packets
- sample global low-overlap historical packets
- include geometry-version-changed packets
- include loop-affected packets

## Related notes

- [[OG-Mapping]]
- [[Octree-Anchor-Hierarchy]]
- [[Progressive-LOD-Growth]]
