# Explicit Birth Event

## Definition

Gaussian/anchor 的出生不由 render gradient densification 触发，而由显式几何事件触发：新观测区域、深度误差、颜色残差（RTG-SLAM 三类事件），或 repeated high-gradient + depth-normal consistent + scale-consistent + dynamic-risk low + not covered（你的 anchor 版本）。

## Why it matters

Gradient densification（原始 3DGS）不可控，不适合在线 SLAM。显式事件让 birth 可以绑定到具体几何证据，可审计、可 gate、可与 temporal/normal/free-space 检查结合。

## Related notes

- [[3dgs-slam/RTG-SLAM]]
- [[Primitive-Lifecycle-State]]
- [[Stable-Unstable-Anchor-Maturity]]
