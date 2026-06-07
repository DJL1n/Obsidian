# Spatial Memory Reconstruction

## Definition

一种 dense 3D reconstruction 范式，通过 external spatial memory 将 previous frame predictions 编码为 key/value features，使新帧可以直接预测到全局坐标系下的 pointmap，无需 test-time global alignment。

## Why it matters

DUSt3R 的 pairwise local pointmap 需要多视角后处理对齐，成本高。Spatial memory 让每帧 pointmap 直接进入 common coordinate，大幅降低重建延迟。且 memory 同时编码 visual + geometry features，支持基于 appearance 和 distance 的检索。

## Key distinctions

| | Pairwise local | Spatial memory global |
|---|---|---|
| 坐标 | per-pair local | initial-frame global |
| 对齐 | test-time optimization | learned memory readout |
| 速度 | 慢 | 快 |
| 长期上下文 | 无 | working + long-term memory |

## Related notes

- [[Spann3R]]
- [[Dense-Sparse-Memory]]
- [[Spann3R-Memory-Architecture]]
