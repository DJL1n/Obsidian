# Isotropic Regularization

## Goal

在在线 3DGS-SLAM 中，通过 isotropic regularisation 惩罚 Gaussian scale vector 与其均值尺度的差异，防止 Gaussians 沿视线方向过度拉长（needle/elongated artifacts）。

## Background

3DGS rasterisation 对光线方向的自由空间没有约束。离线 3DGS 靠多视角充分约束缓解，在线 SLAM 视角少、增量优化时容易产生沿 viewing ray 拉伸的伪影，影响 novel view 和 tracking。

## Procedure

在 mapping loss 中加入 isotropic regularisation term：
- 惩罚 Gaussian 三个 scale 分量的方差
- 鼓励各向同性（更接近球形）
- 不强制完全球形，但防止极端拉伸

## Core insight

GS photometric optimization 会制造几何假象；必须有几何 regularization / certification。这是 MonoGS 对你项目的核心启示之一。

## Related notes

- [[MonoGS]]
- [[GS-Tracking-Representation]]
- [[Gaussian-Covisibility]]
