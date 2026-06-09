# DROID Training Gauge Fix

## Goal

在单目 SLAM 训练中显式处理 gauge freedom（global rotation, translation, scale），使 loss 计算和梯度传播稳定。

## Why it matters

单目 SLAM 只能恢复到 similarity transform。如果训练时不对 gauge 做处理：
- Loss 在可观测等价变换下不稳定
- Linear system conditioning 差
- 梯度传播可能发散

## Procedure

### Gauge fixing strategy

1. **Fix first pose** — 移除 6-DoF gauge freedom（全局旋转 + 平移）
2. **Fix second pose** — 解决 scale freedom

这样每个 training example 的参考系是唯一确定的。

### Training setup
- 每个 training example: 7-frame video sequence
- 预计算 frame pair 的 average optical flow magnitude
- Overlap < 50% 的 pair 设为 infinity distance
- 要求 adjacent frames flow 在 8–96px 之间（避免太容易或太难）

### Loss
1. **Pose loss**: GT pose 和 predicted pose 在 SE(3) log space 的距离
2. **Flow loss**: predicted depth/pose 诱导的 optical flow 与 GT-induced flow 之差
3. 每次 iteration 输出加 loss, γ=0.9 逐步加权

## Comparison with other approaches

| | Gauge fix | Virtual frame | Sim(3) loss |
|---|---|---|---|
| DROID-SLAM | Fix first 2 poses | — | — |
| MASt3R-SLAM | Sim(3) pose in inference | — | — |
| 传统 BA | Fix 1 pose + 1 point | — | — |

## 对 SkelGS-SLAM 的借鉴

DROID 的处理方式表明：更好的方法不是逃避 gauge，而是显式固定 gauge 来稳定训练和优化。这对 packet gauge / scale coherence 的建模方向有直接参考价值。

## Related notes

- [[DROID-SLAM]]
- [[Sim3-Pose-Representation]]
- [[Dense-Bundle-Adjustment]]
