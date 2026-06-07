# Online Global Pose Graph

## Definition

在 learned dense SLAM 中，通过 co-visibility / rigid-flow matrix 构建全局 keyframe graph，在线执行 loop closure 验证和 full bundle adjustment，持续修正历史 keyframes 的 pose 和 dense inverse depth。

## Why it matters

局部 VO/frontend 再强也会 drift。必须有全局 keyframe graph correction。GO-SLAM 证明 DROID-style learned dense geometry（dense flow, confidence, dense inverse depth）可以作为 global BA 的信号源，优于传统 sparse ORB feature-based LC/BA。

## Key distinctions

| | Local VO | Sparse LC/BA | Online learned LC/BA (GO-SLAM) |
|---|---|---|---|
| 范围 | short window | keyframe graph | keyframe graph |
| 信号 | dense flow | ORB features | learned dense flow + confidence |
| Loop 触发 | N/A | BoW retrieval | co-visibility / rigid-flow matrix |
| BA | local | sparse | dense pose-depth joint |
| 在线 | 是 | 是 | 是 |

## Related notes

- [[GO-SLAM]]
- [[Versioned-Geometry-Update]]
- [[GO-SLAM-Architecture]]
