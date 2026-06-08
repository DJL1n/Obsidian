# Multi-Scale Anchor Admission

## Definition

在 anchor skeleton 中，anchor birth / maturity 不是单一阈值，而是多尺度：coarse（大面/物体级）、mid（边界/法线过渡）、fine（高频细节/纹理）。每层有不同证据门槛。OpenMonoGS-SLAM 的 multi-scale SAM supervision + RGBDS-SLAM pyramid + RTG-SLAM lifecycle 可合并为统一 multi-scale anchor admission 设计。

## Why it matters

单一尺度的 anchor 无法同时处理大平面结构和小纹理细节。多尺度让 anchor 先确认大结构稳定，再逐步接受细节。与 RGBDS-SLAM pyramid 和 RTG-SLAM lifecycle 合并使用。

## Related notes

- [[OpenMonoGS-SLAM]]
- [[Compact-Semantic-Memory]]
- [[Semantic-Backend-Regularizer]]
