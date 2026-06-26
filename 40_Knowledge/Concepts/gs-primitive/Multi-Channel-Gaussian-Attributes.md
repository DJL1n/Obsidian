# Multi-Channel Gaussian Attributes

## Definition

在 GS-SLAM 中，每个 Gaussian 不只存储 RGB color，还额外存储 depth value、semantic label、normal、confidence、maturity 等多通道信息，并在 optimization 中联合监督。RGBDS-SLAM 三路渲染（RGB+depth+semantic）是代表实现。

## Why it matters

单一 RGB loss 不足以约束几何和语义。Multi-channel attributes 让不同信号互相补充：depth 提供几何，semantic 提供区域一致性，RGB 提供外观。对你来说可扩展为 anchor maturity / dynamic-risk / normal-stability 等多通道属性。

## Related notes

- [[mapping-reconstruction/RGBDS-SLAM]]
- [[Multi-Level-Pyramid-GS]]
- [[Joint-Multi-Feature-GS-Optimization]]
