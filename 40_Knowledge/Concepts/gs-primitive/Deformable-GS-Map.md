# Deformable GS Map

## Definition

3D Gaussian map 中的每个 primitive 绑定 source (anchor) keyframe。当 keyframe pose 或 proxy depth 更新后，Gaussian mean / scale / rotation 根据投影和 depth 变化主动变形，而非只靠后续 gradient descent 慢慢拉回。Splat-SLAM 是代表实现。

## Why it matters

loop/global BA 会改旧 keyframe pose/depth，旧 geometry 下出生的 Gaussian 会和新几何状态不一致。主动变形保留几何关系，适应 refined poses。消融证明 deformation 明显提升 rendering 质量（Replica office0: PSNR 37.86→41.20）。

## Related notes

- [[Splat-SLAM]]
- [[Geometry-Authority-Separation]]
- [[Proxy-Depth-Packet]]
