# MCBA Cross-View Constraint

## Definition

在多相机 GS-SLAM 中，将同一时刻不同相机之间的 dense reprojection residual (cross-view pairs) 与同一相机不同时间的 temporal residual 统一进同一个 bundle adjustment 框架。MCGS-SLAM 的 MCBA 是代表实现。

## Why it matters

单目 BA 只有 temporal pairs，在车载/机器人场景中视差有限。Cross-view pairs 利用多相机基线和不同视角提供额外几何约束。对单目系统的转化：你也可有不同的几何证据来源（DPVO tracking + depth predictor + anchor repeatability + loop），不只有单一 residual。

## Related notes

- [[MCGS-SLAM]]
- [[Pose-Depth-Co-Versioning]]
- [[JDSA-Scale-Alignment-for-CertifiedPacket]]
