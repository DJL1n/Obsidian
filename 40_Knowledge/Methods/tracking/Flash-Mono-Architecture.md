# Flash-Mono Architecture

## Goal

Predict-and-Refine monocular GS-SLAM: recurrent feed-forward frontend + 2DGS mapping backend + hidden-state loop。

## Components

- Frontend: ViT encoder + hidden state + bidirectional cross-attention + pose MLP + DPT heads → per-pixel 2DGS
- Submap: clip=8, hidden state reset, 1-frame overlap
- Backend: adaptive voxelization → map fusion → prune → 20 iter refine
- Loop: Bag of Hidden States → current frame + historical hidden state → relocalized pose + scale → Sim(3) PGO
- GS correction: keyframe delta → rigid warp 2DGS

## Related notes

- [[Flash-Mono]]
- [[Predict-and-Refine-Gaussian]]
- [[Hidden-State-Submap-Descriptor]]
