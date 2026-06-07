# Sim(3) Pose Representation for Monocular SLAM

## Goal

在单目 SLAM 中显式建模 pose 的 scale 自由度，处理预测 depth/geometry 之间的 scale inconsistency。

## Inputs

- Initial SE(3) pose 估计
- Pointmap 预测（可能有未知或变化的 scale）

## Procedure

### Step 1: Extended pose parameterization
将 pose 表示为 7-DoF 变换：
```
T = {R, t, s}
```
其中 s 是 scale 因子，使得 pose 从 SE(3) 扩展为 Sim(3)（相似变换群）。

### Step 2: Joint optimization
pose update 同时更新 rotation、translation、scale：
```
T_update = {δR, δt, δs}
```

### Step 3: Gauge fixing
固定 first 7-DoF pose 处理 gauge freedom。

## Why Sim(3) matters for MASt3R-based systems

MASt3R/DUSt3R 的 pointmap prediction 中 scale 经常是 inconsistency 的主要来源。即使同一场景的不同 pair 预测，scale 也可能不一致。Sim(3) 表示让 SLAM 系统可以在优化中显式吸收这种不一致性，而不是假设 scale 是稳定隐变量。

## Common failure modes

- Scale 只是被缓解，不是被彻底解决
- 如果 scale 变化过快，优化可能跟不上
- 需要足够的多视角约束才能稳定估计 scale

## Usage in GeometryPacket design

不要假设 MASt3R packet scale 是稳定隐变量。必须把 scale/gauge 显式建模进入 observation model / graph consistency。单靠 scalar 后处理不够。

## Related notes

- [[MASt3R-SLAM]]
- [[Ray-Residual]]
- [[Canonical-Pointmap-Fusion]]
