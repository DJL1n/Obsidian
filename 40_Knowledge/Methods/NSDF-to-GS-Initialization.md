# NSDF to GS Initialization

## Goal

从 neural SDF 中提取 Gaussian splats 的结构初始化参数：center、orientation、opacity、sky。

## Procedure

1. NSDF → marching cubes → surface mesh → mesh vertices → Gaussian centers
2. SDF gradient → normal; principal curvature → second axis; Gram-Schmidt → full orientation frame
3. SDF value + kernel → opacity initialization
4. Map-sized sphere uniform opaque Gaussians → sky/background initialization
5. Fix geometry params → color initialization only (prevent early photo distortion)

## Related notes

- [[GS-SDF]]
- [[SDF-Geometry-Teacher]]
- [[Shape-Regularization]]
