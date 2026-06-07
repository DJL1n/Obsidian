# Structure-Guided GS Completion

## Definition

利用结构先验（Manhattan axes、semantic masks、line boundaries、Gaussian density）在未观测结构面上插入新 Gaussians，补全 holes/gaps。MG-SLAM 的代表性机制。

## Procedure

1. Semantic mask identifies planar surface (floor/ceiling)
2. Line segments define rectangular boundary (Manhattan-aligned)
3. Build 2D grid on hypothesis surface
4. Compute Gaussian density at each grid point
5. If density < threshold → generate new Gaussian center
6. PointNet++ predicts color from surrounding pattern

## Key insight

Observed certified geometry vs hypothesized inferred geometry 必须分开。补全的 Gaussians 可用于 rendering completion，不能反过来强约束 pose。

## Related notes

- [[MG-SLAM]]
- [[Manhattan-Structure-Prior]]
- [[Observed-vs-Hypothesized-Geometry]]
