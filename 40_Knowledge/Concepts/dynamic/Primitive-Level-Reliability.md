# Primitive-Level Reliability

## Definition

每个 map primitive (Gaussian / anchor) 不只存几何和外观参数，还应存一个可学习的或可推导的 reliability / uncertainty state。VarSplat 的 per-splat appearance variance σ² 是代表实现：每个 Gaussian 同时存储 position, opacity, scale, SH color **和** appearance variance。

## Why it matters

Reliability 不应是外部 mask / post-hoc filter，而应是 primitive 的内生属性。低纹理、反光、透明、depth boundary 等区域自然学出高 variance，从而在 tracking/loop/registration 中降权。对应你的 anchor：每个 anchor 应携带 position + normal + scale + confidence + dynamic-risk + appearance variance + support count + error count + birth packet id。

## Related notes

- [[3dgs-slam/VarSplat]]
- [[Appearance-vs-Geometry-Uncertainty]]
- [[AnchorReliability-Admission-Gate]]
