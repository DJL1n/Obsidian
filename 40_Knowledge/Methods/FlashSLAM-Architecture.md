# FlashSLAM Architecture

## Goal

RGB-D GS-SLAM with explicit feature matching + 3D registration tracking, avoiding render-loss dependence for primary pose estimation。

## Components

- Tracking: SuperPoint + LightGlue → RGB-D backprojection → SVD 3D-3D → optional GS refinement
- Depth truncation: 70th percentile per-frame
- Gaussian mapping: standard 3DGS, RGB+depth loss
- Gaussian insertion: where rendered_depth > observed_depth, ICP correction before insert
- Keyframe: visible Gaussian IoU threshold
- Color refinement: loss-weighted sampling

## Related notes

- [[FlashSLAM]]
- [[Explicit-Tracking-vs-Render-Tracking]]
- [[Alignment-Check-Before-Birth]]
