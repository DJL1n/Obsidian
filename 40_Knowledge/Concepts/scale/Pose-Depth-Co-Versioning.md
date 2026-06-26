# Pose-Depth Co-Versioning

## Definition

在 GS-SLAM 中，pose 和 depth 必须是 co-refined 的：只要其中一个变化，geometry packet version 就应该更新。MCGS-SLAM 的 MCBA + JDSA 本质是 pose 和 depth 联合（但不是暴力联合优化，而是交替配合）。

## Why it matters

单独优化 pose 或单独优化 depth 都会破坏几何一致性。对应你的 CertifiedGeometryPacket：T_cw version + depth version + normal version + scale-grid version + confidence version 应作为一个整体。pose 改变了，depth/normal 不能继续使用旧版本。

## Related notes

- [[3dgs-slam/MCGS-SLAM]]
- [[MCBA-Cross-View-Constraint]]
- [[JDSA-Scale-Alignment-for-CertifiedPacket]]
