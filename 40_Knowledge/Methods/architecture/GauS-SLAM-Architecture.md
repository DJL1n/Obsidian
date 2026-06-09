# GauS-SLAM Architecture

## Goal

RGB-D dense SLAM with 2D Gaussian surfels: surface-aware depth rendering + local map front-end + submap backend。

## Components

- Representation: 2D Gaussian surfel (center + tangent + scale + rot + opacity + SH)
- Depth rendering: unbiased ray-surfel intersection + depth adjustment (median surface weighting) + depth normalization
- Tracking: frame-to-local-map, opacity ≥ 0.9 pixel only
- Mapping: surfel attachment (opacity < 0.6), edge growth (0.4–0.6, pseudo-depth)
- Local map: RKF-based, full → backend
- Backend: merge → NetVLAD co-visible → submap BA → pruning (opacity < 0.05) → random opt → final refinement

## Related notes

- [[GauS-SLAM]]
- [[Surface-Aware-Depth-Rendering]]
- [[Local-Map-Tracking]]
