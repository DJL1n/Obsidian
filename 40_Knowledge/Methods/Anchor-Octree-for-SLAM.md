# Anchor Octree for SLAM

## Goal

将 OG-Mapping 的 octree-structured anchor hierarchy 迁移到 monocular GS-SLAM 的 anchor skeleton 中，让 CertifiedAnchor 从 flat list 进化为多尺度 spatial index，ChildGS 按 level-of-detail 受控生成。

## Background

OG-Mapping 证明：anchor 需要空间索引结构。Flat anchor list 长期增长后查询慢、去重难、密度不可控。Octree/voxel hierarchy 提供快速空间索引 + LOD + 结构化 growth。

## Design

### CertifiedAnchorOctree
- Level 0: coarse room/wall/floor support
- Level 1: local surface patches
- Level 2: high-frequency detail / object boundary

### ChildGS birth pipeline
CertifiedGeometryPacket → octree says local anchor support missing + DPVO/MASt3R/depth-normal evidence agrees → add CertifiedAnchor → spawn ChildGS

### Key principles
- 不要从 pixel 直接到 Gaussian
- 应该从 certified local structure 到 Gaussian
- Gaussian growth is not photometric-error-only; it is geometry-certified, level-aware, and anchor-structured

## Contrast

| | Standard GS densification | Anchor octree birth |
|---|---|---|
| 触发 | gradient/error | certified geometry + octree sparsity |
| 粒度 | 无 | level-aware |
| 几何认证 | 无 | CertifiedGeometryPacket |
| 可解释性 | 低 | 高 |

## Related notes

- [[OG-Mapping]]
- [[Octree-Anchor-Hierarchy]]
- [[Progressive-LOD-Growth]]
- [[Dynamic-Keyframe-Window]]
