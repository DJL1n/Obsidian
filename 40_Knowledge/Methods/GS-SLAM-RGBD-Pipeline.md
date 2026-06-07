# GS-SLAM RGB-D Pipeline

## Goal

完整 RGB-D 3DGS SLAM 流程：adaptive Gaussian expansion → coarse-to-fine tracking → stochastic keyframe BA。

## Procedure

### Phase 1: First frame
Uniform 50% pixels → depth back-project → init Gaussians

### Phase 2: Per keyframe
1. Adding: render → detect unreliable pixels → add Gaussians
2. Deletion: project visible Gaussians → check depth → delete floaters
3. Mapping: color + depth loss → optimize Gaussian params

### Phase 3: Tracking (per frame)
Coarse: low-res render → coarse pose
Fine: select reliable Gaussians → full-res render → refine pose

### Phase 4: BA (periodic)
Random keyframes → phase 1 fix pose → phase 2 joint optimize poses + Gaussians

## Related notes

- [[GS-SLAM]]
- [[Adaptive-Gaussian-Expansion]]
- [[Coarse-to-Fine-GS-Tracking]]
