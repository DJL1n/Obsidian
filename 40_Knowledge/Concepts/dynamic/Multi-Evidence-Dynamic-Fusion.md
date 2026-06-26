# Multi-Evidence Dynamic Fusion

## Definition

在 dynamic GS-SLAM 中，融合多个信号源（optical flow anomaly、depth discontinuity、temporal inconsistency、reprojection residual）得到 per-pixel dynamic probability，而非单一 mask。Dy3DGS-SLAM 的 flow + depth + K-means + Bayesian 是代表实现。

## Why it matters

单一信号（flow alone / depth alone / semantic alone）各有盲区。Flow 在低纹理/快速运动不稳，depth 单帧不可靠，semantic 依赖预定义类别。Multi-evidence fusion 让动态检测更鲁棒。Dy3DGS ablation 证明 depth alone ATE 94.8 cm → fusion 3.0 cm。

## Related notes

- [[dynamic-gs/Dy3DGS-SLAM]]
- [[Dynamic-Suppression-Both-Sides]]
- [[DynamicEvidencePacket]]
