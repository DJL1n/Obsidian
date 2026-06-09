# Multi-View Geometry Packet

## Definition

一次 feed-forward 推理直接输出完整多视图几何信息：camera parameters (q,t,f)、depth map、point map、tracking features、confidence。VGGT 是代表实现。

## Why it matters

传统 pipeline 将 pose/depth/point/track 分开估计，误差在各环节累积放大。Multi-view geometry packet 通过共享 backbone 同时预测相关几何量，天然具有内部一致性。VGGT 实验证明去掉 camera/depth/track 中任一损失，pointmap 指标均下降。对应你的 CertifiedGeometryPacket：也是想在 GS 消费前形成一致的 unified geometry candidate。

## Related notes

- [[VGGT]]
- [[Feed-Forward-vs-Optimization]]
- [[VGGT-as-Geometry-Candidate]]
