# Local Map Tracking

## Definition

在 GS-SLAM 中，tracking 不直接在 global Gaussian map 上做，而只在局部可见、近期优化过的 local map 上做。每个 local map 有 reference keyframe。Full 后送给 backend merge。GauS-SLAM 是代表实现。

## Why it matters

Global map 中可能存在遮挡、旧视角、不相关的高 opacity surface 干扰当前帧 tracking。Ablation 证明去掉 local map 后绕物体场景 ATE 从 1.43 cm 恶化到 5.29 cm。对你来说等价于 bounded-lag submap / local CertifiedGeometryPacket window。

## Related notes

- [[GauS-SLAM]]
- [[Surface-Aware-Depth-Rendering]]
- [[Surfel-Anchor-Bridge]]
