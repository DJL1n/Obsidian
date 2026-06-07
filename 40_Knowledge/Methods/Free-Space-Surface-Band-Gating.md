# Free-Space Surface-Band Gating

## Goal

在 GS-SLAM / anchor certification 中，利用 free-space 约束、surface band 约束和 occlusion 约束来 gating Gaussian birth，避免在无支撑区域或不可见区域错误生成 Gaussians。

## Background

ESLAM 的 free-space loss 和 truncation-region loss 展示了如何利用 depth 信息约束 geometry：camera 到 surface 之前的空间应为空，surface 附近有 support band，远离 surface 的区域应弱化。

## Procedure

对 candidate depth surface z_s on ray r：

### Step 1: Free-space check
points z < z_s - τ (camera 到 surface band 之间) → 应为空
- 如果这些点有高置信的占据证据 → candidate 不合法
- 不应在这些位置 birth opaque Gaussians

### Step 2: Surface-band check
points |z - z_s| < τ (surface 附近 truncation band) → 应有 support
- surface-near points: high confidence
- consistent depth, normal, visibility → anchor admission

### Step 3: Tail check
points farther from surface band → weak / ignored
- truncation tail: lower confidence
- 不作为主要证据，也不拒绝 candidate

### Step 4: Occlusion check
inconsistent depth behind visible surface → 不应 birth GS
- 如果 ray 上先遇到一个 surface，后面再有 candidate → occlusion
- occluded region: reject candidate

## 对 SkelGS-SLAM 的借鉴

这比单纯 "depth confidence high → birth Gaussian" 更安全。可以集成到 CertifiedGeometryPacket 的 gate pipeline 中：

```
candidate depth z_s
→ free-space empty?           [gate 1]
→ surface-band support?       [gate 2]
→ occlusion conflict?         [gate 3]
→ consistent normal?          [gate 4]
→ temporal survival?          [gate 5]
→ admit anchor / birth GS
```

## Related notes

- [[ESLAM]]
- [[TSDF-Direct-Supervision]]
- [[Semantic-Cluster-Structure-Prior]]
