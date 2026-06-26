# Submap Geometry Unit

## Definition

将 GS-SLAM 中的 submap 从"窗口"升级为"local geometry unit"：拥有自己的 voxel anchor map、neural Gaussians、local keyframes、submap pose、overlap relation、loop correction transform 和 fusion/distillation 机制的独立几何表示单元。

## Why it matters

窗口/滑动窗口不是 submap。Submap 应有独立空间索引、独立 Grow/Prune 策略、可 deactivate、可 loop corrected、可 fused。VPGS-SLAM 证明这是 large-scale GS-SLAM 的必要结构。

## Key distinctions

| | Sliding window | Submap geometry unit |
|---|---|---|
| 空间索引 | 无 | voxel anchors |
| 独立性 | 窗口共享 | 独立 deactivate |
| Loop correction | 无 | PGO + anchor transform |
| Fusion | 无 | distillation |
| Memory | 常驻 | deactivate 释放 |

## Related notes

- [[slam-frontend/VPGS-SLAM]]
- [[2D-3D-Fusion-Tracking]]
- [[Large-Scale-GS-SLAM-Architecture]]
