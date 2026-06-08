# Splat-SLAM Architecture

## Goal

RGB-only globally optimized dense SLAM + 3DGS: DSPO tracking + proxy depth + deformable 3DGS map。

## Components

- Tracking: dense optical flow → DSPO/DBA (pose + disparity joint opt)
- Monocular depth: scale/shift aligned, high-error region only
- Proxy depth: reliable multi-view + aligned monocular prior
- Loop: flow distance detection
- Global BA: every 20 keyframes
- GS: keyframe proxy depth unproject → Gaussian init
- GS anchoring: each Gaussian bound to source keyframe
- GS deformation: pose/depth update → project → optical-axis adjust → scale/rot update
- GS opt: photometric + proxy-depth + scale reg + exposure comp

## Related notes

- [[Splat-SLAM]]
- [[Geometry-Authority-Separation]]
- [[Deformable-GS-Map]]
