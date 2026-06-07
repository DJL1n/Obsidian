# GSO-SLAM Architecture

## Goal

Real-time monocular dense SLAM: DSO direct VO + 2DGS under EM bidirectional coupling。

## Components

### Frontend
- DSO two-frame direct image alignment → pose
- Keyframe selection: FoV/translation/exposure
- Local BA: pose + brightness + inverse depth + intrinsics

### E-step (parallel thread)
Fix P,D → optimize G。Loss: RGB (L1+SSIM) + semi-dense depth L1 + normal consistency。

### M-step (local BA on keyframe window)
Fix G → optimize P,D。Depth init = weighted avg of DSO depth + GS rendered depth (pseudo-observation)。No full depth rendering per pose update。

### Gaussian Splat Initialization
Image gradients → 2D Σ → multi-KF 3D Σ → eigen → R+S → 2DGS surface splat。

## Related notes

- [[GSO-SLAM]]
- [[EM-VO-GS-Coupling]]
- [[Frontend-Gaussian-Initialization]]
