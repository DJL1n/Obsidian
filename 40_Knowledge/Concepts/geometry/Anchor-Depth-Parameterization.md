# Anchor Depth Parameterization

## Definition

在 SfM/GS-SLAM 中，不把每个像素深度作为自由变量，而是通过 regular anchor grid 绑定局部像素，只优化少量 anchor depth values，保留从 canonical depth 计算的 fixed relative depth offset。MASt3R-SfM 的 pseudo-track 思想。

## Why it matters

自由 per-pixel depth 容易漂移、过拟合、缺乏局部共享约束。Anchor depth 降低自由度（grid spacing=8 时变量减少 ~64×），并强制局部观测共享几何支撑。这和你的 anchor skeleton 方向一致：局部像素绑定到 anchor，共享 depth/normal/confidence。

## Related notes

- [[MASt3R-SfM]]
- [[Constrained-Pointmap]]
- [[Offline-SfM-to-Online-Anchor]]
