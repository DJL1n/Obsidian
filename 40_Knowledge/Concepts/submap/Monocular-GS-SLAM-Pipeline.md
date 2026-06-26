# Monocular GS-SLAM Pipeline

## Definition

一种 monocular GS-SLAM 系统架构：前端用 DPVO 做 sparse temporal tracking，中端用 MVS depth network 估计密集深度，后端用 3DGS 做 dense mapping，通过 SDAR 保持尺度一致。MGS-SLAM 是代表实现。

## Why it matters

纯 GS map-centric monocular tracking（MonoGS style）不稳定。DPVO + GS backend 的分工是更稳健的路线：DPVO 提供 temporal tracking evidence，GS 专注于 dense mapping。这也支持你当前把 DPVO 作为 temporal backbone 的方向。

## Related notes

- [[3dgs-slam/MGS-SLAM]]
- [[Sparse-Dense-Scale-Alignment]]
- [[SDAR-Mechanism]]
