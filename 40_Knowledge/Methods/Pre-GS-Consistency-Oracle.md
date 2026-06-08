# Pre-GS Consistency Oracle

## Goal

将 ADD-SLAM 的 "historical GS map render vs current observation" consistency 思想迁移到你的 pre-certification 阶段，使用 read-only geometry support（DPVO/DROID window + stable anchors + certified candidates）做 dynamic risk 判断，避免 GS map render residual 的反馈污染。

## Design

### Evidence sources (non-GS)
- DPVO/DROID window depth-pose consistency
- Stable anchor observations (temporal repeatability)
- Certified static candidate packets
- Depth-normal consistency
- Temporal repeatability failure
- Support disappearance / exposure cue

### Usage
- Anchor candidate suppression
- CertifiedGeometryPacket admission risk
- Static support weighting
- Tracking residual gating

### Not for
- VideoBuffer writeback
- GS dynamic object modeling
- Anchor identity mutation
- Semantic object-level commitment

## Related notes

- [[ADD-SLAM]]
- [[Consistency-Dynamic-Detection]]
- [[Occlusion-vs-Exposure-Cue]]
