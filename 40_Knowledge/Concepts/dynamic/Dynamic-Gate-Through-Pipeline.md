# Dynamic Gate Through Pipeline

## Definition

在 dynamic GS-SLAM 中，动态过滤不只在 tracking 前做一次 mask，而是贯穿 Gaussian insertion、keyframe selection、pose tracking 和 joint optimization 的完整 pipeline。DGS-SLAM 是代表实现。

## Why it matters

只在 tracking 前做动态 mask，map 仍然可能被动态物体污染。Gate 贯穿 pipeline 意味着：
- Tracking: 动态区域不参与 pose residual
- Insertion: 动态区域不 birth Gaussian
- Window opt: robust mask 持续排除 outlier
- Keyframe: provenance 辅助 loop-aware selection

## Related notes

- [[gs-slam/dynamic/DGS-SLAM]]
- [[Primitive-Birth-Provenance]]
- [[Robust-Mask-Residual]]
