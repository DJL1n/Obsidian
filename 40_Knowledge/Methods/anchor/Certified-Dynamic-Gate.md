# Certified Dynamic Gate

## Goal

将 WildGS-SLAM 的 uncertainty-aware dynamic filtering 思想迁移到你的 CertifiedGeometryPacket 体系中，作为 anchor/candidate admission 的动态风险门。

## Design

### CandidateGeometryPacket fields
- Pose/depth/normal scale evidence
- Uncertainty score (DINOv2 + MLP)
- Dynamic risk score (temporal + multi-view + residual)
- Reliability weight

### Admission rules
- High uncertainty / high dynamic risk → No CertifiedAnchor admission
- No ChildGS birth on uncertain packets
- Quarantine as transient evidence (can re-evaluate later)

### Multi-source fusion
WildGS uncertainty alone not enough. Combine:
- Temporal residual (DPVO)
- Multi-view visibility
- Free-space consistency
- Normal consistency
- Semantic/motion prior
- Anchor survival
- Loop revisiting evidence

## Related notes

- [[WildGS-SLAM]]
- [[Uncertainty-Dynamic-Filtering]]
- [[Static-Dynamic-Side-Channel]]
