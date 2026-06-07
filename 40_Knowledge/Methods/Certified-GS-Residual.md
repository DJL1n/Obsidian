# Certified GS Residual

## Goal

将 GPS-SLAM 的 SDF + residual GS 设计迁移到 monocular setting：先形成 CertifiedGeometryPacket / CertifiedAnchorField 作为稳定几何场，再让 ChildGS 只在这个 field 上作为 appearance residual layer 生长。

## Background

GPS-SLAM 证明：先 geometry (SDF) → 再 GS (residual appearance) 可达 150+ FPS。Monocular 无 SDF，但可用 CertifiedGeometryPacket 替代。

## Design

### Geometry layer (monocular version)
CertifiedGeometryPacket / CertifiedAnchorField = DPVO + MASt3R + depth-normal + free-space + scale + global correction。

### Residual GS layer
ChildGS = appearance residual on certified surface only。Not responsible for geometry。Birth triggered by: certified surface exists + color residual high + geometry stable + local GS density low。

### Gates
- Depth-culling: CertifiedGeometryPacket depth → cull/downweight inconsistent Gaussians
- Shape regularization: anchor-to-child surface alignment (not just center)
- Feedback: gated, weak evaluator only

## Related notes

- [[GPS-SLAM]]
- [[Residual-GS-Layer]]
- [[Geometry-First-GS-Pipeline]]
