# Progressive LOD Growth

## Definition

在 online GS mapping 中，通过 coarse-to-fine level-aware anchor growth 逐步恢复场景细节。Coarse anchors 快速覆盖结构，只有在局部证据（gradient + level insufficiency）反复说明 coarse 不够时，才添加更细层级 anchor。

## Why it matters

Error/gradient-based growth（如 Scaffold-GS）在 offline 场景有效，但 online 中 fine-level Gaussians 没有足够 optimization iterations 来稳定 gradient，容易产生不必要的 anchors。Level-aware growth 使 refinement 有序、可控。

## Procedure

1. 粗 anchor 快速覆盖场景结构
2. 监测 Gaussian gradient per level
3. 如果 gradient > threshold 且当前区域已有同 level anchor
4. → 当前 LOD 不足，提高 granularity
5. 已参与 growth 的 Gaussian 不再用于进一步 anchor growth

## Contrast

| | Error-based growth | Level-aware growth (OG-Mapping) |
|---|---|---|
| 触发 | gradient high | gradient + level hierarchy |
| 新 anchor 位置 | gradient Gaussian 附近 | 更细 voxel 层级 |
| Online 稳定性 | 差 | 好 |
| 粒度控制 | 无 | LOD-aware |

## Related notes

- [[OG-Mapping]]
- [[Octree-Anchor-Hierarchy]]
- [[Dynamic-Keyframe-Window]]
