# LightGlue-SLAM Integration

## Goal

在 SLAM 系统中将 LightGlue 作为 sparse verification / side module 集成，利用其快速、鲁棒的 sparse matching 能力增强 loop closure、relocalization、keyframe-pair validation 等环节。

## Background

LightGlue 不是 SLAM backbone（不输出 depth/pose/BA），但作为 learned sparse matcher 可以补强 DROID/DPVO/MASt3R 不直接覆盖的环节：低成本两视图几何验证。

## Integration positions

### Position 1: Keyframe-pair edge validation
candidate keyframe pair → SuperPoint/ALIKED → LightGlue match → RANSAC essential/PnP/Sim3 → accept/reject geometric edge

### Position 2: Loop closure
retrieval candidate → LightGlue verify → enough matches + RANSAC pass → add pose graph edge

### Position 3: Anchor maturity
多视角下 anchor 附近稳定 LightGlue inliers 计数 → anchor support score

### Position 4: Submap overlap
sparse feature overlap score 作为 cheap pre-check（比跑完整 MASt3R pointmap 轻）

### Position 5: Relocalization
current frame → map keyframes → LightGlue → PnP → pose recovery

## Comparison with alternatives

| | LightGlue | MASt3R | DROID/DPVO |
|---|---|---|---|
| 成本 | 低 (sparse) | 高 (dense pointmap) | 中-高 (dense/sparse correlation) |
| 输出 | sparse matches | dense pointmap + depth | pose-depth + temporal trace |
| 用途 | verification / edge check | geometry proposal | temporal backbone |
| 补充关系 | 低成本 side verification | 强几何 proposal | 主时间骨架 |

## Pipeline example

```
RGB frames
→ DPVO/DROID (temporal tracking, pose)
→ Depth-normal predictor (dense geometry)
→ MASt3R (wide-baseline proposal)
→ LightGlue (sparse pair verification)  ← 插入位置
→ Anchor / CertifiedGeometryPacket (fuse, gate, certify)
→ GS backend
```

## Common pitfalls

- 不要用 LightGlue 替代 temporal optimizer
- 不要用 sparse matches 替代 dense geometry certification
- 动态物体仍需额外处理（RANSAC + semantic mask + temporal residual）
- Feature 提取器选择影响匹配质量

## Related notes

- [[LightGlue]]
- [[Sparse-Learned-Matcher]]
- [[Matchability-Assignment]]
