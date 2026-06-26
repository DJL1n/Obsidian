# Feed-Forward vs Optimization

## Definition

视觉几何的两种范式对比：feed-forward neural prediction（VGGT：一次前向直接输出 camera/depth/pointmap/track，0.2s，SOTA）vs optimization-based（factor graph / BA / bundle adjustment：通过最小化可解释残差迭代求解几何）。

## Why it matters

Feed-forward 快、端到端、neural consistency；但缺残差可审计性、不确定性校准、增量式维护。Optimization 慢但可解释、可在线递推、可 gate。你的系统需要两者结合：DPVO/DROID window 提供可优化 frontend，VGGT 作为 large-window geometry prior / teacher / verifier。

## Related notes

- [[geometry-priors/grounded/VGGT]]
- [[Multi-View-Geometry-Packet]]
- [[VGGT-as-Geometry-Candidate]]
