# Two-View Pointmap Prior

## Definition

DUSt3R/MASt3R 类模型的共享坐标系两视图 pointmap 预测：输入两张图，预测两帧所有像素在共同 3D 坐标系下的 3D 坐标。

## Why it matters

传统 two-view geometry 需要分别做 matching → pose → triangulation。Two-view pointmap prior 把匹配、深度、相对几何都压进一个 learned 网络，直接输出 dense 3D。对 wide-baseline、低纹理、弱标定场景极强。

## Key distinctions

| | Traditional | Two-view pointmap prior |
|---|---|---|
| 流程 | match → pose → triangulate | direct pointmap |
| 匹配 | 需 descriptor | 3D coordinate proximity |
| 几何 | 需 RANSAC | implicit in pointmap |
| 鲁棒性 | 中等 | 高 |

## Common failure modes

- Scale/depth inconsistency between pairs
- Pointmap regression may not be pixel-accurate
- Pair-specific hallucination in unseen regions

## Related notes

- [[geometry-model/MASt3R]]
- [[3D-Grounded-Matching]]
