# DGS-SLAM Architecture

## Goal

RGB-D dynamic GS-SLAM: segmentation + robust residual → dynamic filtering through tracking, insertion, window optimization, keyframe management。

## Components

- Tracking: frame-to-model, dynamic mask + opacity mask, valid static pixels only
- Dynamic mask: Track Anything
- Robust mask: photometric residual histogram → percentile outlier → smoothing
- Gaussian insertion: exclude dynamic regions + invalid depths
- Keyframe mgmt: Gaussian source keyframe ID → loop-aware window re-include
- Backend: joint optimize Gaussian params + keyframe poses, RGB+depth+isotropic loss

## Related notes

- [[DGS-SLAM]]
- [[Dynamic-Gate-Through-Pipeline]]
- [[Primitive-Birth-Provenance]]
