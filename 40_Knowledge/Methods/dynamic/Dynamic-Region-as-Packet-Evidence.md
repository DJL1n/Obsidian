# Dynamic Region as Packet Evidence

## Goal

将 MonST3R geometry-first dynamic detection 的思想转化为你的 anchor 系统的负证据：动态区域不应参与 anchor maturity / GS birth，只能作为 per-timestep observation。

## Design

### Geometry-first dynamic rule
DPVO/DROID flow ≈ camera-motion-induced flow (from depth + pose)
- 一致 → static / reliable → 可进入 anchor maturity / GS admission
- 不一致 → dynamic / unreliable → 不进入 persistent map

### Evidence sources
- DPVO track residual
- depth-induced flow vs optical flow
- multi-view reprojection residual
- depth-normal consistency

### Decision
动态证据强的区域 → candidate anchor 不晋升 mature，GS birth 推迟或拒绝。动态物体只作为 per-timestep observation，不污染 persistent anchor / GS。

## Related notes

- [[MonST3R]]
- [[Per-Timestep-Pointmap]]
- [[Geometry-First-Dynamic-Detection]]
