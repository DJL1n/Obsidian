# AnchorReliability Admission Gate

## Goal

将 VarSplat 的 primitive-level reliability 思想转化为你的 anchor admission gate：每个 anchor 的可靠性评分决定它是否晋升成熟 / 参与 CertifiedGeometryPacket。

## Design

### AnchorReliability Score
```
temporal_repeatability
+ depth_scale_consistency
+ normal_stability
+ local_flow_support
- dynamic_risk
- appearance_variance (from GS backend, not frontend)
- occlusion_boundary_risk
```

### Decision
- **Low** → weak candidate only, not mature anchor
- **Medium** → local support only, not in CertifiedGeometryPacket
- **High** → mature read-only anchor support, can participate GS birth / packet admission

### Two-layer design
Pre-GS (frontend): GeometryReliability via DPVO/DROID residual + depth-normal consistency + MASt3R pairwise + anchor repeatability。
Post-GS (backend): AppearanceReliability via VarSplat-style learned variance, not back-propagated to pose/depth/anchor。

## Related notes

- [[VarSplat]]
- [[Primitive-Level-Reliability]]
- [[Appearance-vs-Geometry-Uncertainty]]
