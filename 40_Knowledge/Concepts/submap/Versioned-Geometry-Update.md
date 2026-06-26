# Versioned Geometry Update

## Definition

在 dense SLAM 系统中，当 global BA / loop closure 更新了 keyframe poses 和 depths 后，mapping 必须对 geometry change 做出响应：选择 pose/depth 变化最大的 keyframes 更新 map，而非继续使用旧 geometry。

## Why it matters

如果 tracking 的 pose/depth 被全局修正但 map 仍按旧 pose/depth 写入，reconstruction 会不一致。GO-SLAM 的 instant mapping 明确 snapshot 最新 poses/depths，选择变化最大的 keyframes 更新，保证 map 始终反映最新几何。

## Keyframe selection strategy

1. Latest two keyframes（最新信息）
2. Keyframes not yet optimized by mapping（未处理）
3. Pose/depth change top 10（变化最大）
4. Stratified sampled 10 historical keyframes（防遗忘）

## 对 SkelGS-SLAM 的启发

每个 keyframe/packet 应有 geometry version。当 global BA 改了 pose/depth → 对应 packet version 变更 → submap/GS 需知道哪些 keyframes 变化最大 → 不应继续使用旧 geometry 当作真值。这就是 CoVersionedGeometryPacket 的系统级理由。

## Related notes

- [[slam-frontend/GO-SLAM]]
- [[Online-Global-Pose-Graph]]
- [[Global-Correction-Mapping]]
