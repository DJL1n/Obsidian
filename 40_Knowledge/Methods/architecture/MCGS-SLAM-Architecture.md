# MCGS-SLAM Architecture

## Goal

Multi-camera RGB 3DGS-SLAM: MCBA (temporal + cross-view) + JDSA + multi-camera GS mapping。

## Components

- Rig: synchronized calibrated multi-camera, body pose shared
- Keyframe: multi-camera keyframe (RFT flow threshold)
- Depth/normal: Metric3Dv2
- JDSA: per-pixel/grid scale alignment, 与 BA 交替
- MCBA: temporal + cross-view dense BA, body pose + inverse depth, weighted photometric, Schur complement
- GS mapping: refined depth → unproject → init → densify/prune
- Pose-consistent GS: keyframe anchor → GS transform propagation
- GS loss: RGB + depth + normal + scale reg, unbiased intersection depth
- Offline: global BA + joint pose+exposure+GS refinement

## Related notes

- [[MCGS-SLAM]]
- [[MCBA-Cross-View-Constraint]]
- [[Pose-Depth-Co-Versioning]]
