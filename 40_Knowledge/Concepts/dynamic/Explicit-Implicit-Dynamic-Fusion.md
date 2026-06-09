# Explicit-Implicit Dynamic Fusion

## Definition

在 dynamic GS-SLAM 中，动态 mask 的来源不只有 open-world / semantic segmentation（显式），还包括 rendering residual、uncertainty、depth inconsistency 等隐式证据。两者融合得到更精细的 dynamic region / dynamic keypoint mask。LVD-GS 是代表实现：Grounded SAM 显式 + DINO-depth uncertainty 隐式。

## Why it matters

纯语义分割会漏掉非典型动态物体、遮挡边界、阴影；纯 residual mask 可能误判低纹理/反光区域。显式+隐式融合更鲁棒。支持你的判断：动态去除不应做成独立语义模块，应嵌入几何成熟度判断。

## Related notes

- [[LVD-GS]]
- [[Multi-Representation-Geometry-Admission]]
- [[Multi-Evidence-Anchor-Admission]]
