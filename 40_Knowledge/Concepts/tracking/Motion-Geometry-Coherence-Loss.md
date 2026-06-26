# Motion-Geometry Coherence Loss

## Definition

在几何优化中，引入运动约束作为 pose-depth 一致性的认证信号：比较由当前 pose/depth 预测的亮度/光流变化与实际观测到的运动（event / optical flow / temporal gradient）是否一致。EAG3R 的 event-based photometric consistency loss 是代表实现。

## Why it matters

如果 pose/depth 正确 → predicted motion ≈ observed motion。如果 pose/depth 错了 → 两者不一致。这提供了一个独立于 RGB photometric residual 的一致性检查。可用于你的 CertifiedGeometryPacket 认证：candidate packet 需通过 motion-geometry coherence test。

## Related notes

- [[gs-slam/dynamic/EAG3R]]
- [[SNR-Aware-Reliability-Gating]]
- [[Event-Augmented-Geometry-Candidate]]
