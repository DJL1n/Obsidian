# GS Birth Gate

## Definition

一种受控的 Gaussian seeding 策略：不依赖 vanilla 3DGS 的 gradient split/clone，而是通过 rendered alpha mask、nearest-neighbor sparsity check 和 color gradient sampling 来决定是否在某个区域添加新 Gaussian。

## Why it matters

Vanilla 3DGS 的 gradient-based densification 在 SLAM 中容易导致 Gaussian 失控生长、冗余和几何错误。Controlled seeding 使用 RGB-D observation + alpha low region + NN sparsity check 来新增 Gaussian，数量更可控、几何更可靠。

## Procedure

1. **Alpha mask check**: render active map → rendered alpha < threshold → 未覆盖或稀疏区域
2. **Nearest-neighbor check**: candidate point 在 search radius 内无邻居 → 真正需要新 Gaussian
3. **Color gradient sampling**: 高梯度区域优先（边缘/纹理细节）
4. **Initialization**: anisotropic Gaussian, scale 由 NN distance 定义

## Contrast

| | Vanilla 3DGS | Controlled seeding |
|---|---|---|
| 生长信号 | gradient high | alpha low + NN sparse |
| 密度控制 | 弱 | alpha threshold + NN radius |
| 几何依赖 | 无 | RGB-D depth |
| 适合 SLAM | 否 | 是 |

## Related notes

- [[3dgs-slam/Gaussian-SLAM]]
- [[Submap-Gaussian-Organization]]
