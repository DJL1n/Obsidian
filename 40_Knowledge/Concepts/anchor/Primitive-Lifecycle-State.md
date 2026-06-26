# Primitive Lifecycle State

## Definition

GS/anchor primitive 在在线 SLAM 中具有生命周期状态：unstable（新出生/拟合差，参与优化）、stable（充分收敛，只读）、outlier（长期不稳定，删除）。RTG-SLAM 的 stable/unstable Gaussian 是代表实现。

## Why it matters

不是所有地图单元都需要持续优化。在线系统应将计算集中在未稳定区域。RTG-SLAM 证明 state management 能显著降低优化成本。直接对应你的 anchor lifecycle：unstable = candidate；stable = read-only certified；outlier = quarantine/delete。应记录 confidence count / error count / birth frame / last observed。

## Related notes

- [[gs-slam/monocular/RTG-SLAM]]
- [[Explicit-Birth-Event]]
- [[Stable-Unstable-Anchor-Maturity]]
