# Tri-Plane Feature

## Definition

用三组正交的 2D feature planes（XY, XZ, YZ）存储场景特征。对任意 3D 点 p = (x, y, z)，投影到三个平面做 bilinear interpolation，取到的三个特征向量相加后输入 decoder。

## Why it matters

相比 3D voxel grid（O(L³)），tri-plane 将 memory growth 降到 O(3L²)，在保持表达能力的同时大幅降低显存和计算成本。ESLAM 用此替代 NICE-SLAM 的 voxel grid。

## Key distinctions

| | Voxel grid | Tri-plane |
|---|---|---|
| Memory growth | O(L³) | O(3L²) |
| 特征存储 | (x, y, z) | (x, y), (x, z), (y, z) |
| 表达能力 | 完整 3D | 投影到 3 个 2D 平面，略有折损 |
| 插值方式 | trilinear | bilinear x3 + sum |

## Common failure modes

- 大场景仍需 O(L²)，不是 sublinear
- 细长结构或高度倾斜的 surface 可能不如 voxel grid 精确
- 3 个平面求和可能丢失单平面特有的局部信息

## Related notes

- [[ESLAM]]
- [[TSDF-Direct-Supervision]]
