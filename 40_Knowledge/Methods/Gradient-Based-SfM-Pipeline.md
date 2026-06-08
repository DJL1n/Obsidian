# Gradient-Based SfM Pipeline

## Goal

将 MASt3R pairwise 3D prior 扩展到完整 SfM pipeline：retrieval → sparse graph → local reconstruction → coarse 3D alignment → fine 2D reprojection refinement + anchor depth。

## Components

1. MASt3R encoder features → ASMK retrieval
2. Sparse scene graph: keyframe core + NN edges
3. MASt3R decoder on edges → local pointmaps + matches
4. Constrained pointmap: raw → pinhole model
5. Coarse: 3D matching loss (Adam, confidence-weighted)
6. Fine: 2D reprojection error (robust loss, anchor depth)

## Key insight
Not online SLAM. Not dynamic. 但 constrained pointmap + anchor depth 可直接借到你的 pre-certification 体系。

## Related notes

- [[MASt3R-SfM]]
- [[Constrained-Pointmap]]
- [[Anchor-Depth-Parameterization]]
