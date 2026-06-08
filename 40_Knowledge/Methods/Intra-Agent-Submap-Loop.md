# Intra-Agent Submap Loop

## Goal

将 CoGS-SLAM 的 inter-agent loop closure 思想迁移到单机器人的 inter-submap 场景：当前 keyframe/submap 重新看到了历史 mature anchor/static support → retrieval + consistency verification + anchor reactivation + packet verification。

## Design

1. Retrieval candidate (descriptor / MASt3R / DROID consistency)
2. Depth-normal agreement check
3. Dynamic-risk check
4. Anchor support reactivation
5. Packet consistency verification (geometry + appearance)

Intra-agent 但 inter-submap 的 loop verification 可以在不引入多机器人的情况下，提升 anchor maturity 和 submap consistency。

## Related notes

- [[CoGS-SLAM-Survey]]
- [[Submap-Level-Provenance]]
- [[Geometry-vs-Visual-Consistency]]
