# Certified Geometry Field

## Goal

将 GS-SDF 的 LiDAR-NSDF geometry teacher 思想迁移到 monocular setting。构造一个"弱但可认证"的几何场，让 ChildGS 只在这个场约束范围内生长。

## Background

GS-SDF 中 NSDF 是 Gaussian 的几何 teacher。Monocular 无 LiDAR，但可构造 CertifiedGeometryField / CertifiedAnchorField 作为替代。

## Sources

CertifiedGeometryField 的证据来源：
- DPVO patch survival + residual + confidence
- MASt3R/DUSt3R pairwise pointmap agreement
- Depth-normal predictor consistency
- Free-space / surface-band checks
- Scale/gauge graph
- S3LAM-like primitive support
- Spann3R memory proposal
- Multi-view reprojection consistency

## Usage

- ChildGS initialization: from certified field surface, not raw predicted depth
- ChildGS regularization: shape-level alignment to certified field, not just center
- Geometry refinement: bounded by certificate; if residual grows → quarantine/demote

## Related notes

- [[GS-SDF]]
- [[SDF-Geometry-Teacher]]
- [[Shape-Regularization]]
- [[NSDF-to-GS-Initialization]]
