# Monocular GS Insertion Strategy

## Goal

在没有真实 depth 的 monocular 设置下，通过渲染出的 depth 和 median depth 初始化新 3D Gaussians，并通过 visibility pruning 清理不稳定 Gaussians。代表"先 birth 后 prune"的经典策略。

## Background

MonoGS 的 monocular insertion 策略：没有真实 depth → 从当前 Gaussian map 渲染 depth → 有 depth estimate 处低方差初始化 → 无 depth estimate 处 median depth 高方差初始化。大量初始位置错误，靠后续优化和 pruning 清理。

## Procedure

### Insertion
1. Render current Gaussian map → depth image
2. For pixels with depth estimate: init Gaussian near that depth, low variance
3. For pixels without depth estimate: init Gaussian at median rendered depth, high variance

### Pruning
- Gaussians inserted within last 3 keyframes
- Not observed by ≥3 other frames → prune (geometrically unstable)

## Contrast with your approach

| | MonoGS | Your direction |
|---|---|---|
| 策略 | insert first, prune later | certify first, birth later |
| Depth 来源 | rendered / median | certified geometry packet |
| Pruning | visibility count | pre-birth gate |
| 安全性 | 低（大量错误初始） | 高（gate-controlled） |

## Core insight
MonoGS 的 monocular insertion 暴露了"先 birth 后 prune"的风险。你的改进空间正是在 GS 之前加入 geometry certification：让 Gaussian birth 从 rendered-depth guess 变成 certified-anchor/certified-packet driven。

## Related notes

- [[MonoGS]]
- [[Isotropic-Regularization]]
- [[GS-Tracking-Representation]]
