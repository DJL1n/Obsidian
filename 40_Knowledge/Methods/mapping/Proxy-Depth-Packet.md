# Proxy Depth Packet

## Goal

在 RGB-only GS-SLAM 中构造 proxy depth：reliable multi-view depth (DSPO/DBA) 作为 authority，monocular depth prior 只作为 high-error / missing 区域的 scale/shift-aligned prior。Splat-SLAM 的设计。

## Design

1. DBA: optimized disparity → reliable depth
2. Cross-keyframe consistency check → high/low-error classification
3. Monocular depth: scale/shift aligned to reliable multi-view
4. Proxy depth: reliable multi-view + aligned monocular for high-error only
5. DBA objective 和 monocular objective 交替优化

## Your system adaptation
可改为 CertifiedGeometryPacket depth：
- 可靠 multi-view / window depth → authority
- Predictor depth → weak prior (scale aligned, only for weak areas)
- 冲突/不一致区域 → suppress, not write back

## Related notes

- [[Splat-SLAM]]
- [[Geometry-Authority-Separation]]
- [[Deformable-GS-Map]]
