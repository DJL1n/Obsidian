# Recurrent Correction Update

## Definition

RAFT-style 的 recurrent update 机制：ConvGRU 反复读取 correlation volume 中的 visual evidence，逐轮修正 dense correspondence field，同时结合上一轮的 BA residual 作为 feedback。

## Why it matters

它让系统不是在单次 forward 中预测最终匹配，而是通过多轮迭代逐步精化。每一轮都利用当前几何状态 + 视觉相似度 + 上一轮优化结果，形成 recurrent optimisation trace。

## Key distinctions

| | One-shot matching | Recurrent correction |
|---|---|---|
| 匹配方式 | 单次预测 | 多轮迭代精化 |
| Feedback | 无 | previous BA residual |
| 信号 | static | temporal optimisation trace |
| 可解读性 | 一次性置信度 | 每轮 revision flow + confidence + residual |

## 每轮迭代的信号

ConvGRU 输入：
1. correlation lookup features（在预测位置附近查询视觉相似度）
2. current flow（当前 pose/depth 诱导的 dense correspondence field）
3. previous BA residual（上一轮优化后的残差）
4. context features
5. hidden state

ConvGRU 输出：
- revision flow r_ij: 网络认为当前投影需要修正多少
- confidence map w_ij: 每个 residual 的权重
- damping λ: 深度更新阻尼
- upsampling mask

## 对 anchor 方向的价值

每轮迭代都产生可追踪的信号：
- 哪些像素的 r_ij 持续减小（收敛）
- 哪些像素的 w_ij 持续高（可信）
- 哪些像素的 BA residual 持续低（几何一致）
- 哪些区域反复被判定不可信（潜在动态/不稳定）

## Common failure modes

- 需要足够迭代次数才能收敛（DROID 默认 10 轮初始化）
- Correlation volume 大 → lookup 成本高
- 大 motion 或 occlusion 时可能需要更多迭代

## Related notes

- [[slam-frontends/neural-correspondence/DROID-SLAM]]
- [[Dense-Bundle-Adjustment]]
- [[Correlation-Volume]]
