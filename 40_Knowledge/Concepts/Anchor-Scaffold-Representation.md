# Anchor-Scaffold Representation

## Definition

一种结构化的 3D Gaussian 表示方式：用稀疏 anchors 作为 scaffold/骨架，每个 anchor 局部生成 k 个 neural Gaussians，Gaussian 属性由 anchor feature + 视角方向 + 距离动态预测。

## Why it matters

Vanilla 3DGS 的 free Gaussian cloud 容易冗余、view-dependent 效果差、存储大。Anchor-scaffold 把高斯从"自由粒子"升级为"受 anchor 约束的局部派生物"，实现更紧凑、视角自适应的表示。

## Key distinctions

| | Free Gaussian (3DGS) | Anchor-scaffold (Scaffold-GS) |
|---|---|---|
| 组织 | 独立自由 | anchor-conditioned |
| 属性 | 固定优化 | view-dependent 动态预测 |
| 存储 | 所有参数显式存 | anchor + small MLP |
| 生长 | densification/split | gradient + observation gate |
| 视角适应 | SH color 硬拟合 | MLP 动态解码 |

## Common failure modes

- 强依赖初始 SfM 点云质量
- 纯 rendering gradient 驱动生长，非几何认证
- View-adaptive geometry 可能掩盖真实几何错误

## Related notes

- [[Scaffold-GS]]
- [[ChildGS-Local-Derivation]]
- [[Anchor-Growing-Pruning]]
