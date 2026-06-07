# HI-SLAM2 Architecture

## Goal

RGB-only monocular dense Gaussian SLAM: DROID-like tracking + monocular depth/normal priors + JDSA scale alignment + online PGBA + 3DGS mapping + Gaussian deformation + offline full BA + joint refinement。

## Components

### Online tracker
- DROID-like recurrent optical flow
- Keyframe graph BA (pose + inverse depth)
- Monocular depth prior → JDSA alignment
- Normal prior → GS geometry supervision

### Online loop closing (PGBA)
- Loop candidates: flow distance + orientation + frame gap
- Per-keyframe scale correction in PGBA
- Post-PGBA: depth scale update + Gaussian deformation

### Continuous mapper
- Aligned depth → backproject → Gaussian means
- Losses: photometric + depth + normal (cosine) + scale reg
- Densification/pruning interleaved

### Offline refinement
- Post-keyframe insertion (coverage gaps)
- Full BA (all overlapping pairs)
- Joint pose-map refinement (Adam + rasterization pose Jacobians)

## Related notes

- [[HI-SLAM2]]
- [[JDSA-Scale-Alignment]]
- [[Gaussian-Map-Deformation]]
