# ChildGS Local Derivation

## Goal

在 SLAM-GS 系统中实现 parent anchor → child Gaussian 的结构化派生，替代"depth pixel/point → 直接 birth Gaussian"的自由生长模式。

## Background

Scaffold-GS 证明 child Gaussians 从 anchor 局部派生可以显著减少冗余、提升视角适应性。但对 SLAM 场景，anchor certification 必须基于几何证据而非纯渲染梯度。

## Design

### Parent anchor
- position (certified)
- normal / tangent frame
- confidence score
- local feature
- local scale
- support observation list
- temporal survival

### Child Gaussian derivation
μ_i = x_anchor + T_anchor · o_i

其中 T_anchor 是 local tangent/normal frame，o_i 是局部 offset（learnable）。这样 child Gaussian 的生长受 anchor local surface 约束，而非全局自由漂移。

### Child attributes
- anisotropic scale (from anchor scale + offset)
- opacity (view-adaptive, but capped by anchor confidence)
- color / SH (view-adaptive allowed)
- rotation (constrained by anchor normal frame)

### Key constraints
- Geometry attributes (position, scale, rotation) = slow-changing, multi-view consistent
- Appearance attributes (color, SH) = can be view-adaptive
- Child Gaussians cannot exceed anchor support region
- No free-floating birth outside certified anchors

## Related notes

- [[Scaffold-GS]]
- [[Anchor-Scaffold-Representation]]
- [[Anchor-Growing-Pruning]]
