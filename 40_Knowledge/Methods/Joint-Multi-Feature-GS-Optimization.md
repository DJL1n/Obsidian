# Joint Multi-Feature GS Optimization

## Goal

在 GS backend 中，将 RGB、depth、semantic 等多通道 attribute 纳入 joint loss 共同优化，互相约束。RGBDS-SLAM 的 TCMF-RO 是代表实现。

## Design

### Loss structure
- RGB: L1 + SSIM
- Depth: L1
- Semantic: L1 + SSIM
- Total: weighted combination

### Timing
必须放在"已认证几何之后"，不能让它反向污染前端。

### 可替换
语义不一定是类别标签，可替换为 dynamic-risk / anchor maturity / normal-stability。

## Related notes

- [[RGBDS-SLAM]]
- [[Multi-Level-Pyramid-GS]]
- [[Multi-Channel-Gaussian-Attributes]]
