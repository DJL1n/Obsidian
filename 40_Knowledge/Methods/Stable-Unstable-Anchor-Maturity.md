# Stable-Unstable Anchor Maturity

## Goal

将 RTG-SLAM 的 stable/unstable Gaussian state management 迁移到你的 anchor skeleton，实现 anchor 生命周期：unstable candidate → stable read-only support → outlier。

## Design

| State | Conditions | Allowed ops |
|---|---|---|
| **Unstable candidate** | New, low confidence count, depth/normal not confirmed | Evaluator; may participate in packet but not in certified geometry |
| **Stable read-only** | Confidence count ≥ threshold, multi-frame depth-normal consistency, dynamic-risk low | Read-only static support; can serve as CertifiedGeometryPacket source; geometry not mutated |
| **Outlier** | Long-term unstable, repeated error, dynamic-risk high | Quarantine or delete |

### Per-anchor provenance
- birth frame ID, birth packet ID
- confidence count, error count
- last observed frame
- normal stability / depth-scale stability
- dynamic-risk score

## Related notes

- [[RTG-SLAM]]
- [[Primitive-Lifecycle-State]]
- [[Explicit-Birth-Event]]
