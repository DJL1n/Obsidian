# DynamicEvidencePacket

## Goal

将 Dy3DGS-SLAM 的 multi-evidence dynamic fusion 改造成 anchor 系统中可审计的只读 dynamic evidence field。

## Design

per-pixel:
  p_static
  p_dynamic
  flow_anomaly_score
  depth_discontinuity_score
  depth_temporal_inconsistency
  reprojection_residual_score

per-anchor:
  static_log_odds
  temporal_repeatability
  depth_normal_stability
  dynamic_contamination_score

## Decision rules

- p_static low → no anchor birth / no GS admission
- p_static medium → weak observation only, not in CertifiedPacket
- p_static high + temporal_repeatability high → anchor maturity evidence

## Related notes

- [[Dy3DGS-SLAM]]
- [[Multi-Evidence-Dynamic-Fusion]]
- [[Dynamic-Suppression-Both-Sides]]
