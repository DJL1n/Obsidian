# Event-Augmented Geometry Candidate

## Goal

将 EAG3R 的 event-augmented geometry 思想转化为你的 CertifiedGeometryPacket 的额外认证信号：如果事件/运动/flow 与 pose/depth 预测不一致，则 candidate 不应通过 admission。

## Design

### Motion-geometry coherence test
- Predicted: pose+depth → motion field → brightness / flow change
- Observed: event / optical flow / temporal gradient
- Compare → coherence score

### Decision
- High coherence → 可考虑 admission
- Low coherence → 动态/低可靠 → 拒绝或降低 candidate 权重

### Without event camera
可用 optical flow / feature track residual / temporal gradient 作弱替代。

## Related notes

- [[EAG3R]]
- [[SNR-Aware-Reliability-Gating]]
- [[Motion-Geometry-Coherence-Loss]]
