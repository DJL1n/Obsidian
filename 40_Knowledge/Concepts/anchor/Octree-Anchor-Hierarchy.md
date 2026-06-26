# Octree Anchor Hierarchy

## Definition

一种多尺度 spatial index 结构用于 GS-SLAM 中的 anchor 管理：使用 sparse octree 将场景空间划分为 coarse-to-fine 层级，每个 voxel center 作为 anchor，anchor 按 level 管理，growth 按 level hierarchy 控制。

## Why it matters

Flat anchor list 长期增长时：查询慢、去重困难、密度不可控、多尺度难管理、birth/prune 不稳定。Octree 提供快速空间索引 + 多尺度 LOD + 结构化 growth 控制。

## Key distinctions

| | Flat anchor list | Octree anchor hierarchy |
|---|---|---|
| 查询 | 慢 | fast spatial indexing |
| 密度控制 | 无 | level-aware |
| 去重 | 困难 | voxel-level |
| 多尺度 | 不支持 | coarse-to-fine LOD |
| Growth | gradient-driven | level hierarchy + gradient |

## Related notes

- [[mapping/structured/OG-Mapping]]
- [[Progressive-LOD-Growth]]
- [[Dynamic-Keyframe-Window]]
