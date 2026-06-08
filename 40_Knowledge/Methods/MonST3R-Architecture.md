# MonST3R Architecture

## Goal

Dynamic video geometry via DUSt3R pointmap + dynamic fine-tune + video-specific global optimization。

## Components

- Backbone: DUSt3R (frozen encoder, fine-tuned decoder+head)
- Data: PointOdyssey + TartanAir + Spring + Waymo
- Per-timestep pointmap: each timestep has own pointmap
- Pose: same-view 2D-3D + PnP + RANSAC + confidence mask
- Static region: optical flow ≈ camera-motion-induced flow
- Global opt: sliding window + L_DUSt3R + λ L_smooth + λ L_flow (static regions)
- Output: video depth, camera pose, intrinsics, static mask, time-varying point cloud

## Related notes

- [[MonST3R]]
- [[Per-Timestep-Pointmap]]
- [[Geometry-First-Dynamic-Detection]]
