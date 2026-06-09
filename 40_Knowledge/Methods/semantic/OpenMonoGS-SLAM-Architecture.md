# OpenMonoGS-SLAM Architecture

## Goal

Monocular RGB-only open-set semantic 3DGS-SLAM: MASt3R tracking + SAM masks + CLIP + memory bank。

## Components

- Geometry: MASt3R-SLAM tracking + pointmap
- GS: standard 3DGS + per-Gaussian learnable semantic feature
- Semantic: SAM multi-scale masks + CLIP embeddings
- Memory: online memory bank (low-dim feature + high-dim CLIP prototypes, attention readout)
- Loss: photometric + multi-view semantic contrastive + CLIP regression
- Extras: additional mapping frames (tracking keyframes 太稀疏)

## Related notes

- [[OpenMonoGS-SLAM]]
- [[Compact-Semantic-Memory]]
- [[Multi-Scale-Anchor-Admission]]
