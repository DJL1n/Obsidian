# Gaussian Map Deformation

## Definition

在 GS-SLAM 中，当 PGBA / full BA 更新了 keyframe poses 后，同步变形 3D Gaussian primitives 的 mean、orientation 和 scale，以保持 map global consistency。HI-SLAM2 的机制：anchor keyframe pose change → child Gaussian inherits transform。

## Why it matters

很多 GS-SLAM 容易忽略：pose/depth changed → map must update。如果不做 Gaussian deformation，loop/BA 后的 Gaussian map 会与修正后的 pose/depth 不一致，造成 geometry inconsistency。

## Related notes

- [[gs-slam/monocular/HI-SLAM2]]
- [[JDSA-Scale-Alignment]]
- [[Certified-JDSA]]
