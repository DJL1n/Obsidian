# GO-SLAM Architecture

## Goal

一个三线程 dense SLAM 架构：front-end tracking（含 loop closure）+ back-end full BA + instant SDF mapping，实现全局一致的在线 dense reconstruction。

## Threads

### Thread 1: Front-end tracking
- DROID-style recurrent update → current pose/depth
- Keyframe selection（flow threshold）
- Loop closure: co-visibility matrix → edge selection → suppression → DBA correction

### Thread 2: Back-end tracking
- Separate thread
- Full BA over all historical keyframes
- Keyframe graph reconstructed from co-visibility + temporal adjacency
- Edge suppression 控制冗余
- 运行中持续修正历史 keyframes

### Thread 3: Instant mapping
- Snapshot latest keyframe poses/depths
- Select keyframes: latest 2 + unoptimized + pose-diff top 10 + stratified 10 historical
- Update SDF hash grid + color MLP（NeuS rendering）
- 不 refine pose/depth（信任 BA 输出）

## Key design choices

- Full BA 和 tracking 分离 → front-end 不阻塞
- Mapping 对 geometry change 响应 → 防止 map/pose 不一致
- Loop closure 基于 dense flow consistency → 非 ORB BoW

## Related notes

- [[GO-SLAM]]
- [[Online-Global-Pose-Graph]]
- [[Versioned-Geometry-Update]]
