# Patch Temporal Optimization

## Goal

基于 DPVO 的 recurrent patch tracking + differentiable BA 方法论，设计可追踪、可解释的 temporal optimization trace，用于 anchor lifecycle 信号提取。

## Background

DPVO 的核心优化循环：current pose/depth → reproject patch → local correlation → temporal convolution → graph aggregation → factor head (δ, Σ) → differentiable BA → updated pose/depth。

## Key components

### 1. Local correlation
与 DROID 的全局 dense correlation 不同，DPVO 在 geometric reprojection 附近做 7×7 局部搜索。对 anchor 方向来说，这意味着匹配信号始终约束在几何合理范围内。

### 2. 1D temporal convolution
沿 patch trajectory 传播信息。允许网络建模 patch appearance 随时间变化，并让 BA residual / confidence 具有 temporal continuity。

### 3. Graph-based aggregation
Patch aggregation（同 patch 跨帧交流）+ Frame aggregation（同源 patches 同帧交流）。让 patch 之间可以共享上下文信息。

### 4. Factor head output
每条 edge 输出 δ（2D trajectory revision）和 Σ（confidence weight）。这两个量可以直接作为 anchor quality signal：
- δ 小且稳定 → geometry consistent
- Σ 高且稳定 → tracking reliable

### 5. Differentiable BA
优化 poses + patch inverse depths。BA residual 和 depth update magnitude 可以直接反映 patch 的几何稳定性。

## 对 SkelGS-SLAM 的借鉴

不是照搬 DPVO 的网络结构，而是吸收其 optimization trace 设计：
- 每条 edge 有 revision + confidence
- 每个 patch 有可追踪的 lifecycle
- BA 提供 residual + update magnitude
- Temporal conv 提供连续性

这些信号可以直接映射到 anchor admission/rejection gate。

## Related notes

- [[DPVO]]
- [[DPVO-Temporal-Frontend]]
- [[Patch-Graph-VO]]
- [[Patch-Lifecycle]]
