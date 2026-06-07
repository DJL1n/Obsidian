# Patch Lifecycle

## Definition

VO 中每个 patch 从诞生到退出优化窗口的完整生命周期，包含 source frame、connected frames、trajectory revisions、confidence weights、inverse depth updates、BA residuals、survival time 等信息。

## Why it matters

很多 SLAM/GS 系统中，前端信号是一次性的（如 LightGlue matches）或密集但无法追踪单点生命周期（如 DROID dense depth）。Patch lifecycle 提供了最自然的 anchor maturity evidence：patch 活了多久、多稳定、多可信。

## Lifecycle signals

- **source frame**: patch 诞生的帧
- **connected frames**: patch 投影到哪些帧
- **trajectory revisions δ**: 网络认为投影需要修正多少
- **confidence Σ**: 每条 edge 的 learned weight
- **inverse depth history**: depth 是否反复大幅变化
- **BA residual**: 是否被同一 pose-depth 解释
- **survival time**: 在窗口内存在多久
- **visibility count**: 在多少 connected frames 中稳定可见
- **exit reason**: 窗口退出 / 动态判断 / 低置信度

## Contrast with alternatives

| | Pairwise match | Dense depth | Patch lifecycle |
|---|---|---|---|
| Temporal | 无 | dense 但无单点 ID | 有 |
| Confidence | ratio test | 无 | learned Σ |
| Survival | N/A | N/A | 有 |
| 适合 anchor | 否 | 否 | **是** |

## Related notes

- [[DPVO]]
- [[Patch-Graph-VO]]
- [[DPVO-Temporal-Frontend]]
