# TSDF Direct Supervision

## Definition

用 RGB-D depth measurement 直接构造 signed distance supervision，而不依赖 volume rendering 间接学习 surface。对每个有 depth 的 ray，free-space 点约束 TSDF → +1，surface band 内点约束 signed distance 与 measurement 一致。

## Why it matters

Occupancy / density 需要通过 volume rendering 间接学习 surface，收敛慢。TSDF 可以直接从 depth 构造 per-point loss，使 geometry 更快收敛，surface 质量更高。

## Loss components

- **Free-space loss L_fs**: camera center 到 surface 之前的点 → TSDF → 1
- **Truncation-region loss L_T**: surface 附近 truncation band 内的点，用 depth 近似 signed distance
- **Rendered depth loss L_d**: rendered depth 与 depth sensor 一致

## Key distinctions

| | Occupancy/density | TSDF direct |
|---|---|---|
| 监督信号 | volume rendering 间接学习 | depth measurement 直接构造 |
| 收敛速度 | 慢 | 快 |
| Surface 质量 | 一般 | 更好 |
| 依赖 | RGB-D depth | RGB-D depth |

## Common failure modes

- 需要可靠 depth sensor，noisy/missing depth 影响大
- Truncation distance T 的选择影响收敛质量
- 动态物体 depth 不一致会污染 TSDF

## Related notes

- [[mapping-reconstruction/ESLAM]]
- [[Free-Space-Surface-Band-Gating]]
