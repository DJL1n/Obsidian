# Occlusion vs Exposure Cue

## Definition

在 dynamic GS-SLAM 中，通过比较 observed depth 与 rendered (historical) depth 的大小关系，区分"新遮挡"（动态物体移动到背景前）和"暴露背景"（动态物体移开后露出原遮挡背景）。

## Why it matters

单纯 depth inconsistency residual 会把两者混在一起。区分后：
- Observed depth < rendered → 新前景遮挡 → 不适合生成静态 anchor
- Observed depth > rendered → 暴露背景 → 可等待多帧确认后补充静态 anchor

## Source

ADD-SLAM 利用这个区分来做 dynamic-static separation 和背景填补。

## Related notes

- [[gs-slam/dynamic/ADD-SLAM]]
- [[Consistency-Dynamic-Detection]]
- [[Dynamic-Static-Composite-Mapping]]
