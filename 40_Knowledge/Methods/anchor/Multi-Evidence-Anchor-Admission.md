# Multi-Evidence Anchor Admission

## Goal

将 LVD-GS 的 multi-representation evidence + explicit-implicit dynamic fusion 转化为你的 anchor admission gate：candidate anchor 需通过多层证据 maturity 检查才能晋升成熟。

## Design

### Evidence layers
1. Tracking repeatability (DPVO/DROID)
2. Depth stability (跨帧一致性)
3. Normal consistency
4. Image gradient / texture
5. DINO feature consistency (read-only, 不进 pose)
6. Optical-flow consistency
7. Dynamic uncertainty (implicit residual + explicit semantic)

### Decision
- All layers pass → mature read-only anchor, GS birth admissible
- Some layers fail → weak candidate / transient only
- Dynamic evidence high → reject from persistent map

## Related notes

- [[LVD-GS]]
- [[Multi-Representation-Geometry-Admission]]
- [[Explicit-Implicit-Dynamic-Fusion]]
