# Appearance vs Geometry Uncertainty

## Definition

在 GS-SLAM 中，appearance uncertainty（颜色/纹理/SH 观测稳定性）和 geometry uncertainty（depth/pose/scale 几何可信度）应分开建模。VarSplat 主要建模 appearance variance；你的 CertifiedGeometryPacket 需要的是 geometry reliability。

## Why it matters

VarSplat 的 per-splat variance 主要通过 photometric residual 学习，虽然 depth residual 也参与，但它不是完整的 geometry uncertainty（pose covariance / depth scale / normal bias / dynamic probability）。你的系统需要显式区分两层：GeometryReliability 控制 anchor admission / packet certification；AppearanceReliability 控制 GS backend rendering 权重，不反向污染几何。

## Related notes

- [[gs-slam/structured/VarSplat]]
- [[Primitive-Level-Reliability]]
- [[AnchorReliability-Admission-Gate]]
