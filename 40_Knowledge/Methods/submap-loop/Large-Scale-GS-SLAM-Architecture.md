# Large-Scale GS-SLAM Architecture

## Goal

将 GS-SLAM 从 room-scale 扩展到 indoor/outdoor large-scale 场景的系统化设计：multi-submap + voxel-anchor representation + 2D-3D fusion tracking + BEV loop closure + online submap distillation。

## Components

### Multi-submap
Global = {S_1, ..., S_n}。Each: voxel anchors, neural Gaussians, keyframes, submap pose。Non-essential submaps deactivate。

### Voxel-anchor representation
Point cloud → voxelize → anchors → MLP-decoded neural Gaussians。Multi-resolution voxel size: fine near, coarse far。

### 2D-3D fusion tracking
2D photometric coarse + 3D ICP fine + adaptive selection。

### BEV loop closure + PGO
BEVPlace++ descriptor → rendering loss + voxel ICP → PGO → update poses + Gaussian anchors。

### Online distillation submap fusion
Overlapping submaps → render RGB/depth → distillation loss → align rendered outputs。

## Related notes

- [[VPGS-SLAM]]
- [[Submap-Geometry-Unit]]
- [[2D-3D-Fusion-Tracking]]
- [[Submap-Fusion-Distillation]]
