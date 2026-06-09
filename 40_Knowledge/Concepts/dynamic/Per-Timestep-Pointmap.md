# Per-Timestep Pointmap

## Definition

在动态场景几何估计中，不为整个视频建一个统一静态几何，而是为每个时间点独立预测 pointmap。动态物体在不同时刻占据不同 3D 位置，自然形成 time-varying point cloud。MonST3R 是代表实现。

## Why it matters

传统方法先 decompose 成 depth / flow / motion mask / pose 再拼接，每步累积误差。Per-timestep pointmap 直接估计每个时刻的 geometry，不强迫动态物体满足静态一致性。对 GS-SLAM 的启发：动态物体只应作为 per-timestep observation，不进入 persistent anchor / Gaussian map。

## Related notes

- [[MonST3R]]
- [[Geometry-First-Dynamic-Detection]]
- [[Dynamic-Region-as-Packet-Evidence]]
