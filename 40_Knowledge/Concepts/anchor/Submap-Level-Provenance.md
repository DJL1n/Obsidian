# Submap-Level Provenance

## Definition

在 GS-SLAM 中，每个 submap/geometry packet 应携带 complete provenance：packet_id、source frames、pose version、depth predictor version、normal estimator version、anchor support list、dynamic-risk evidence、maturity state、last validated time。CoGS-SLAM 的多 agent local submap → global alignment 是典型场景。

## Why it matters

单帧 geometry packet 不足以支持 loop closure、submap fusion、asynchronous update 和 GS backend consumption。Provenance 使每个 packet 可追溯、可验证、可版本化管理。对应你的 CoVersionedGeometryPacket 方向。

## Related notes

- [[survey/CoGS-SLAM-Survey]]
- [[Geometry-vs-Visual-Consistency]]
- [[Intra-Agent-Submap-Loop]]
