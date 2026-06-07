# Certified EM Coupling

## Goal

将 GSO-SLAM 的 EM bidirectional coupling 思想迁移到你的 CertifiedGeometryPacket 体系，形成安全的双向信息交换。

## Design

### E-step (map update)
- Update GS only from CertifiedGeometryPacket / CertifiedAnchor
- Not from raw frontend depth / raw GS render feedback

### M-step (frontend update)
- CandidateGeometry update using gated GS evidence only
- Not direct VideoBuffer writeback
- Require: temporal residual + normal/free-space consistency + dynamic check + geometric version agreement

### Safety principles
- GS renders → candidate/evaluator, not truth
- Frontend depth → candidate proposal, not GS birth truth without certificate
- Both sides can suggest, but certification layer decides admission

## Related notes

- [[GSO-SLAM]]
- [[EM-VO-GS-Coupling]]
- [[Frontend-Gaussian-Initialization]]
