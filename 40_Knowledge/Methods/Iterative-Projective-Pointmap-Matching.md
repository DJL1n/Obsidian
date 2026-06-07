# Iterative Projective Pointmap Matching

## Goal

在两帧 dense pointmap 之间高效建立 pixel-level 匹配，避免全局 brute-force O(N²) 搜索。

## Inputs

- Reference frame 的 ray image: `{ray_ref(u) | u ∈ pixels}`
- Source frame 的 3D pointmap: `{X_cur(v) | v ∈ pixels}`
- 初始 match 估计（如有）

## Procedure

### Step 1: Ray projection
对于 source frame 的每个 3D 点 X，用当前 pose T 变换到 reference frame：
```
X' = T * X
```
目标是在 reference image 中找到像素 u，使得 `ray_ref(u)` 与 `normalize(X')` 方向最接近。

### Step 2: Iterative optimization
独立优化每个点的 pixel coordinate u，最小化 angular error：
```
min_u angular_error(ray_ref(u), normalize(X'))
```
用 analytical Jacobians + Levenberg-Marquardt 求解，通常 10 次内收敛。

### Step 3: Incremental acceleration (tracking)
Tracking 时用上一帧 matches 作为初始化，加速收敛。

### Step 4: Outlier filtering
3D distance 太大的 matches 标记为 outlier 并失效。

### Step 5: Feature refinement（可选）
几何投影得到初始 match 后，用 MASt3R per-pixel features 在局部 patch window 中做 coarse-to-fine 搜索，选择 feature similarity 最大的位置。

## Assumptions

- Pointmap 质量足够好，ray field 连续可微
- 初始 pose 估计不差太远

## Failure modes

- Pointmap prediction 严重错误时，ray projection 不准确
- 大面积 occlusion 时 matches 减少
- 纯旋转场景需要额外处理

## Performance

| Variant | Time |
|---|---|
| w/o feature refinement | ~0.5 ms |
| w/ feature refinement | ~2 ms |
| 原始 MASt3R dense matching | ~2000 ms |

## Related projects

- [[MASt3R-SLAM]]
- [[Ray-Camera-Model]]
- [[Ray-Residual]]
