# GSFusion Architecture

## Goal

Online RGB-D mapping: TSDF geometry + 3DGS appearance, quadtree-controlled GS birth, keyframe-based online optimization。

## Components

- TSDF: octree-based (Supereight2), single-resolution, running weighted average
- 3DGS: standard representation, quadtree + TSDF voxel check for init
- Quadtree: RGB contrast-based recursive decomposition, large cell for low-texture area, small cell for edges
- GS birth: quadrant center backproject → nearest voxel weight check → new only if weight=1
- GS init: position from centre, scale from cell depth span, SH from RGB
- Opt: differentiable rendering, photometric loss online
- Keyframe mgmt: non-keyframe → sample random keyframes for anti-forgetting
- Global opt: optional post-scan

## Related notes

- [[GSFusion]]
- [[Geometry-Gated-GS-Birth]]
- [[Hybrid-TSDF-GS-Map]]
