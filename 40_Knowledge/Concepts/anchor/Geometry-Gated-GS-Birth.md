# Geometry-Gated GS Birth

## Definition

Gaussian 的出生不由 render loss / gradient densification 驱动，而由显式几何承载结构（TSDF voxel / anchor / certified support）的 maturity 和 occupancy check 控制。GSFusion 的 quadtree + TSDF voxel check 是代表实现。

## Why it matters

Gradient densification 不可控，容易导致 Gaussian 膨胀、漂浮、几何不一致。Geometry gate 让 birth 可审计、可 gate、可抑制冗余。对应到你的系统：anchor / certified geometry maturity → GS admission，不是 photometric residual → densification。

## Related notes

- [[mapping/structured/GSFusion]]
- [[Quadtree-Image-Budget]]
- [[TSDF-Geometry-Gate]]
