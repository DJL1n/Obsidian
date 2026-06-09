# Surfel-Anchor Bridge

## Goal

将 GauS-SLAM 的 2D Gaussian surfel 思想迁移到你的 anchor 体系：anchor/surface support → birth 2D Gaussian surfel（normal/tangent/scale/confidence from certified packet）→ GS 只优化 appearance + small geometry correction。

## Design

- Anchor provides: position, normal, local tangent frame, scale/support radius, depth confidence, view coverage
- Child primitive: 2D Gaussian surfel (not free 3D ellipsoid)
- Geometry: surfel orientation from anchor normal, scale from anchor support
- GS role: appearance optimization + small local offset; no geometry authority

## Related notes

- [[GauS-SLAM]]
- [[Surface-Aware-Depth-Rendering]]
- [[Local-Map-Tracking]]
