# Online GS Seeding Strategy

## Goal

在在线 GS-SLAM 中通过受控的 Gaussian seeding 策略替代 vanilla 3DGS 的 gradient-based densification，确保 Gaussian 数量可控、几何来自可靠观测。

## Background

Vanilla 3DGS 的 densification 不适合 SLAM：gradient split/clone 导致 Gaussian 失控，SLAM 需要更保守的 seeding。Gaussian-SLAM 提出从 RGB-D 观测出发的 controlled seeding。

## Procedure

### Step 1: Keyframe point cloud generation
RGB-D → back-project → dense 3D point cloud

### Step 2: First keyframe seeding
- Uniform samples for overall coverage
- High color-gradient samples for edge/detail

### Step 3: Subsequent keyframe seeding
1. Render active sub-map → alpha map
2. Alpha low → unseen/reconstructed region
3. Sample RGB-D points from these regions
4. Nearest-neighbor search against active sub-map
5. No neighbor in search radius → seed new Gaussian

### Step 4: Optimization adjustment
- 至少 40% iterations 分配新 keyframe
- Isotropic regularization 防止 elongated Gaussians
- Depth loss 确保几何正确

## Contrast with vanilla 3DGS

| | 3DGS densification | Online seeding |
|---|---|---|
| 触发条件 | gradient threshold | alpha low + NN sparse |
| 新增位置 | 梯度大的 Gaussian 附近 | RGB-D 观测的稀疏区域 |
| 几何先验 | 无 | RGB-D depth |
| 密度控制 | split/clone 不受限 | alpha + NN radius |
| 适合在线 | 否 | 是 |

## Related notes

- [[Gaussian-SLAM]]
- [[GS-Birth-Gate]]
- [[Submap-Gaussian-Organization]]
