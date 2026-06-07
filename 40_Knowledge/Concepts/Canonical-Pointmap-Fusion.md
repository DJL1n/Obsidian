# Canonical Pointmap Fusion

## Definition

多帧 MASt3R pointmap 通过 weighted average 融合为一个 keyframe 的 canonical pointmap，作为该 keyframe 的局部稳定几何表示。

## Why it matters

单次 MASt3R two-view prediction 有噪声，且存在 scale/depth inconsistency。多视角融合利用多个 viewpoint 平均掉噪声，提高 canonical pointmap 的 coherence 和稳定性。

## Key distinctions

| | Single prediction | Canonical fusion |
|---|---|---|
| 噪声 | 高 | 低 |
| Scale 一致性 | 不稳定 | 多帧平均后更稳 |
| 更新方式 | 一次性 | running weighted average |
| 副作用 | 无 | 同时 filter camera model（rays 定义） |

## Common failure modes

- Fusion 只是 keyframe-local，不是全局一致
- 不输出 GS-ready uncertainty
- 对动态物体无显式处理，dynamic object 会污染 fusion

## Usage in pipeline

```
raw MASt3R packet
  → candidate packet
  → fused canonical packet (加权平均融合)
  → GS birth packet
```

GS 不应该直接吃 raw two-view output，而应该吃经过 temporal/multiview fusion 的 canonical geometry。

## Related notes

- [[MASt3R-SLAM]]
- [[Ray-Camera-Model]]
