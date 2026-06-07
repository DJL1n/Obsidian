# GPS-SLAM Architecture

## Goal

Ultra-fast RGB-D reconstruction via SDF fusion (geometry + base color + tracking) + residual Gaussian splats (high-frequency appearance correction)。

## Components

### SDF layer
Truncated distance + initial RGB on surface。InfiniTAM-style fusion。

### Gaussian layer
Sparse residual Gaussian radiance field。Added in high color-error regions only。~50% fewer Gaussians, ~75% fewer optimization iters。

### Rendering
Stage 1: SDF raycast → depth + color
Stage 2: Depth-culling Gaussian splatting (order-independent) → residual color overlay

### Tracking
SDF-based alignment / ICP。

### Pipeline
```
RGB-D → SDF ICP tracking → SDF fusion
→ detect color error → add/remove Gaussians
→ optimize residual Gaussians (photometric)
→ SDF raycast + Gaussian splat → final image
```

## Related notes

- [[GPS-SLAM]]
- [[Residual-GS-Layer]]
- [[Geometry-First-GS-Pipeline]]
