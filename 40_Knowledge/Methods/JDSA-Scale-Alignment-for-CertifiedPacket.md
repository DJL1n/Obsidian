# JDSA Scale Alignment for CertifiedPacket

## Goal

将 MCGS-SLAM 的 JDSA depth-scale alignment 思想转化为你的 CertifiedGeometryPacket 的 scale certification 环节：depth predictor 输出只是 candidate，需要独立的 scale alignment + consistency check 后才能进入 certified geometry。

## Design

### 问题
Depth predictor (Omnidata/MoGe/Metric3Dv2) 即使有 metric scale，仍存在跨帧/跨视角 scale inconsistency。直接使用会导致 GS map 伪影和 geometry inconsistency。

### 方案
D_pred → per-pixel/grid scale factor → D_scaled = s(u) · D_pred → consistency check → certified depth。

scale alignment 应与 BA 交替，不暴力联合优化。

### Certification gates
- Scale consistency across local window
- Normal consistency
- Temporal reprojection consistency
- Multi-view depth consistency
- Anchor support agreement

通过后才进入 CertifiedGeometryPacket / GS birth。

## Related notes

- [[MCGS-SLAM]]
- [[MCBA-Cross-View-Constraint]]
- [[Pose-Depth-Co-Versioning]]
