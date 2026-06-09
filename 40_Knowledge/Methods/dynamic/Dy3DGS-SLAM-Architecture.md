# Dy3DGS-SLAM Architecture

## Goal

Monocular RGB dynamic GS-SLAM: optical flow + monocular depth → fusion mask → dynamic suppression in both tracking and GS mapping。

## Components

- Optical flow mask: U-Net motion segmentation
- Depth mask: DepthAnythingV2
- K-means: cluster multiple dynamic objects
- Bayesian fusion: P(dynamic|depth,flow) ∝ P(depth|dyn)·P(flow|dyn)·P(dyn)
- Tracking: ResNet50 pose net + motion loss + depth scale constraint + local BA
- GS: standard 3DGS, dynamic pixel → depth infinity pruning, color+depth loss with dynamic penalty

## Related notes

- [[Dy3DGS-SLAM]]
- [[Multi-Evidence-Dynamic-Fusion]]
- [[Dynamic-Suppression-Both-Sides]]
