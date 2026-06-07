# Manhattan Structure Prior

## Definition

将 Manhattan World 假设（室内结构表面和线条与三组正交方向对齐）引入 GS-SLAM：用于 tracking（线段比点更稳）、mapping（line photometric loss）和 scene completion（平面 Gaussian 补全）。

## Why it matters

室内结构化场景中大量墙体/地面/天花板低纹理，点特征不稳。线段更符合人工结构，能在 textureless areas 提供可靠约束。Manhattan 假设让系统可以补全未观测平面，减少 holes。

## Key distinctions

| | No structure prior | Manhattan prior |
|---|---|---|
| Textureless tracking | 弱 | 线特征约束 |
| Planar completion | 无 | 假设驱动 |
| 适用场景 | 通用 | 室内人工结构 |
| 风险 | 无 | 非 Manhattan 场景错误 |

## Related notes

- [[MG-SLAM]]
- [[Structure-Guided-GS-Completion]]
- [[Observed-vs-Hypothesized-Geometry]]
