# Dense Bundle Adjustment

## Definition

对 camera poses 和 per-pixel inverse depth 的联合优化，使用 Gauss-Newton 求解，所有 residual 由网络预测的 corrected correspondence 和 confidence 加权构成。

## Why it matters

传统 BA 优化 camera poses + sparse 3D landmarks。Dense BA 将优化扩展到逐像素深度，让 pose 和 depth 在同一个几何框架中联合更新，而不是先估 pose 再估 depth 或反之。

## Key distinctions

| | Sparse BA | Dense BA (DROID) |
|---|---|---|
| 优化变量 | poses + 3D landmarks | poses + per-pixel inverse depth |
| 观测 | feature matches | corrected dense correspondence |
| 权重 | 固定 / robust kernel | learned confidence w_ij |
| 求解 | Schur complement | Schur complement (depth diagonal) |

与 BA-Net 的区别：BA-Net 优化 depth basis 系数，DROID 直接优化逐像素 depth，不受限于预定义 depth basis。

## 求解方式

1. 目标函数局部线性化
2. Gauss-Newton 求解 Δξ 和 Δd
3. Hessian 有 block structure（每个 residual 只涉及一个 depth variable）
4. Schur complement 消元 depth → 解 reduced camera system → 恢复 depth update
5. 训练时在 PyTorch 计算图中可反向传播
6. 推理时 custom CUDA kernel + sparse Cholesky decomposition

## Common failure modes

- Dense BA 计算成本高，长序列需 memory-efficient 实现
- 动态区域高 residual 会污染深度更新
- Per-pixel depth 缺乏表面平滑先验，thin structures 不稳定

## Related notes

- [[DROID-SLAM]]
- [[Recurrent-Correction-Update]]
- [[Correlation-Volume]]
