# UP-SLAM Architecture

## Goal

RGB-D dynamic GS-SLAM: ORB-SLAM3 tracking + training-free uncertainty + probabilistic octree anchors + DINO feature-enriched GS map + temporal encoding + parallel tracking-mapping。

## Components

### Tracking thread
- ORB-SLAM3 tracking
- Training-free uncertainty from multi-modal residuals
- Motion mask → filter dynamic keypoints

### Mapping thread (parallel)
- Probabilistic octree anchors (Bayesian update)
- DINO feature distillation: low-dim anchor feature → shallow MLP → high-dim feature
- Uncertainty MLP (DINO-based, independent gradients)
- Temporal encoding (sinusoidal positional encoding → condition MLPs)
- Structured GS decoding: anchor feature + view → MLP → Gaussian attributes
- Losses: color + depth + feature + uncertainty

## Related notes

- [[UP-SLAM]]
- [[Probabilistic-Anchor-Update]]
- [[Multi-Modal-Uncertainty]]
