# Submap Fusion Distillation

## Goal

在 GS-SLAM loop closure 后，通过 online distillation 对齐重叠 submaps 的 rendered RGB/depth，实现比单纯 pose 对齐更深层的 map fusion。

## Background

VPGS-SLAM 中 loop closure 后多个 submaps 可能描述同一区域。只对齐 submap pose 不够，因为内部 Gaussian representation 可能不同、冗余或冲突。Online distillation 让 submaps 的 rendered output 互相对齐。

## Procedure

1. 找到 loop closure 涉及的重叠 submaps
2. 定位高度相似的 image pairs / overlapping regions
3. 分别用不同 submap 渲染 RGB 和 depth
4. 构造 distillation loss
5. 让 submaps 的 rendered RGB/depth 互相对齐
6. 融合重复信息，提高 global consistency

## Related notes

- [[VPGS-SLAM]]
- [[Large-Scale-GS-SLAM-Architecture]]
- [[Submap-Geometry-Unit]]
