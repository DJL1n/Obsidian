# Frequency Pyramid Growth

## Goal

在 structured GS-SLAM 中通过 coarse-to-fine frequency pyramid 控制 Gaussian 分布，低频约束大尺度结构，高频允许细节生长，避免无控制 densification。

## Procedure

- Coarse level: 低频结构 / 大尺度外观
- Fine level: 高频细节 / edge / texture
- Pyramid: Gaussian distribution layered by frequency band

## 与 OG-Mapping / VPGS 的关系

OG-Mapping: octree level / progressive anchor refinement
VPGS: multi-resolution voxel anchors
SEGS-SLAM: frequency regularization pyramid

核心都是 Gaussian growth 应该是分层、受控的。

## Related notes

- [[SEGS-SLAM]]
- [[Structured-GS-for-SLAM]]
- [[Appearance-Geometry-Decoupling]]
