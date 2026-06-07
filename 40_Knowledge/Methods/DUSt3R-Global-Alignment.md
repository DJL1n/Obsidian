# DUSt3R Global Alignment

## Goal

将 DUSt3R 的 pairwise pointmaps（不同 pair 在不同坐标系）通过优化变换参数，统一表达到一个共同 reference frame。

## Background

DUSt3R 的基本单元是 image pair。多图时各 pair 坐标系不同，需要 global alignment。Spann3R 的出发点正是避免这步。

## Procedure

1. 对每对 (Ii, Ij) 运行 DUSt3R → pairwise pointmaps in frame i
2. 优化 pair transformations / camera variables
3. 将所有 pointmaps 表达在共同 reference frame 下

## Key limitations

- 如果 pairwise pointmaps 有 scale drift / 局部错误 / 低重叠 / 动态物体 → alignment 可能被拉坏
- 多视角仍不是完全 feed-forward

## Related notes

- [[DUSt3R]]
- [[Pointmap-Regression]]
- [[Uncalibrated-Dense-Reconstruction]]
