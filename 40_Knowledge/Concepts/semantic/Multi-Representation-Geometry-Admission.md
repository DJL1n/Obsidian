# Multi-Representation Geometry Admission

## Definition

在 GS-SLAM 中，不只用 RGB/depth/photometric residual 判断几何可靠性，而是融合 geometry + semantic + DINO feature + residual uncertainty + dynamic evidence 等多层表示决策 anchor maturity / GS birth。LVD-GS 的 Sem-Geo-DINO 是代表实现。

## Why it matters

单一 RGB/depth residual 在室外动态、低纹理、复杂场景中不足。Multi-representation 提供 redundancy 和 cross-disciplinary consistency。对应你的 anchor admission：candidate anchor 需通过 tracking repeatability + depth stability + normal consistency + texture + DINO consistency + flow consistency + dynamic uncertainty → admit/reject。

## Related notes

- [[gs-slam/multi-sensor/LVD-GS]]
- [[Explicit-Implicit-Dynamic-Fusion]]
- [[Multi-Evidence-Anchor-Admission]]
