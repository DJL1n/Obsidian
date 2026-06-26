# Hybrid TSDF-GS Map

## Definition

在 RGB-D mapping 中同时维护 TSDF volumetric map（几何骨架、机器人任务）和 3D Gaussian map（视觉渲染、高质量外观）。GSFusion 是代表实现：TSDF voxel 作为 Gaussian 初始化和去重的空间约束。

## Why it matters

纯 GS-SLAM 容易 geometry inconsistency、Gaussian count explosion。TSDF 提供稳定的几何底座，Gaussian 只负责视觉细节。这种 hybrid 设计比纯 GS 更工程稳健，且对 downstream robot tasks 更友好。

## Related notes

- [[mapping-reconstruction/GSFusion]]
- [[Geometry-Gated-GS-Birth]]
- [[Quadtree-Image-Budget]]
