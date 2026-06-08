# LVD-GS Architecture

## Goal

LiDAR-Visual 3DGS-SLAM for large-scale dynamic outdoor scenes: hierarchical representation collaboration + explicit-implicit dynamic modeling。

## Components

- Input: RGB + LiDAR point cloud
- Geometry: LiDAR projection → DepthLab densification
- Representation: Sem (Grounded SAM) + Geo (LiDAR depth) + DINO features
- Rendering: RGB + depth + semantic + DINO (semantic detached, DINO similarity)
- 3DGS init: from LiDAR points
- Dynamic: open-world seg (explicit) + DINO-depth uncertainty → residual motion (implicit)
- Pose: multi-scale loss + scan-to-map registration (KISS-ICP-like)
- Map: localized submaps

## Related notes

- [[LVD-GS]]
- [[Multi-Representation-Geometry-Admission]]
- [[Explicit-Implicit-Dynamic-Fusion]]
