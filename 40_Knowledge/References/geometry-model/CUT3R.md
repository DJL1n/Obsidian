---
tags:
  - Foundation-Model
  - Dense-Correspondence
  - Point-Cloud
  - Online
---
# CUT3R: Continuous 3D Perception Model with Persistent State

> CVPR 2025 Oral. 论文整理笔记。
> ## 0. 一句话结论

CUT3R 是一个带持久状态的连续 3D 感知模型。核心是 stateful recurrent Transformer：每帧更新内部 state，在线输出 metric-scale pointmaps 到共同坐标系。不是 SLAM pose graph / DROID recurrent BA / GS-SLAM backend / SDF mapper。

定位：continuous recurrent pointmap predictor。

---

## 1. 系统定位

Feed-forward / recurrent 3D foundation model + persistent state + online dense pointmap reconstruction + general 3D/4D perception。

---

## 2. 核心问题

DUSt3R/MASt3R pairwise 强但缺少 persistent scene state，多视图需对齐，视频累积易 drift。CUT3R 回答：让模型自己携带 persistent state，每帧更新，直接输出 common-coordinate pointmaps。

---

## 3. Input/Output

### Input
RGB image stream / unordered photo collection（varying length, static/dynamic）

### Output
Metric-scale pointmaps in common coordinate + confidence + coherent dense scene reconstruction + virtual unseen region inference

---

## 4. Persistent State

State_{t-1} + image_t → state_t + pointmap_t。内部 latent scene representation，非显式地图/pose graph/GS map。随每个 observation 演化。

vs DUSt3R: pairwise stateless
vs Spann3R: external spatial memory → CUT3R 是 recurrent state

---

## 5. 与其他模型关系

| 模型 | 机制 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| DUSt3R | pairwise pointmap | foundation prior |
| MASt3R | + matching head | pairwise witness |
| Spann3R | external spatial memory | memory proposal |
| SLAM3R | I2P+L2W registration | window proposal |
| **CUT3R** | **persistent recurrent state** | **continuous stateful proposal** |
| DPVO/DROID | BA recurrent | temporal backbone |

---

## 6. 强项

1. **Stateful** — 比 pairwise DUSt3R 更适合视频
2. **Common-coordinate pointmaps** — 可直接累积
3. **支持 static/dynamic/varying-length** — 更通用
4. **可 infer unseen regions** — virtual view probing

---

## 7. 局限

1. **Persistent state 不可审计** — latent, 无显式残差/置信度
2. **无显式 BA / pose graph / loop closure** — 长序列 drift
3. **Metric-scale 仍需验证** — 不能直接当 GS truth
4. **Dynamic content 支持 ≠ static map certification**
5. **不能直接提供 anchor lifecycle** — 无 DPVO-style trace

---

## 8. 对 SkelGS-SLAM 的启发

### 可作为 recurrent dense geometry proposal

### Persistent state → 你的 anchor memory 概念
CUT3R latent state 的思想可显式化为 CertifiedGeometryMemory:
- Recent candidate packets
- Mature certified anchors
- Long-term sparse anchor skeleton
- Geometry-version history
- Loop-affected packets

与 CUT3R 区别：你的系统 explicit + auditable + gateable + rollbackable。

### Unseen inference → Hypothesis, not Certified
CUT3R 推断的区域应标记为 HypothesizedGeometry，只用于 weak rendering completion，不强约束 pose。

### CUT3R state 可作为 failure detector

你之前 CUT3R all-frames PSNR 13.93 说明直接当 GS input 危险。合理位置：candidate witness，不是 direct GS source。

---

## 9. 建议系统位置

```
     → CUT3R (recurrent dense proposal)
     → MASt3R (pairwise witness)
     → SLAM3R/Spann3R (window/memory proposal)
     → Global graph (consistency)
     → CertifiedGeometryPacket (fuse+gate)
     → CertifiedAnchor/ChildGS
     → GS backend (residual rendering)
```

CUT3R 角色：persistent-state geometry proposal，不是 system truth。

---

## 10. 最终定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **CUT3R** | **persistent state pointmap** | **continuous stateful proposal** |
| DUSt3R | pairwise pointmap | foundation |
| MASt3R | + matching | pairwise witness |
| Spann3R | memory pointmap | memory proposal |
| SLAM3R | window+registration | window proposal |
| GS-SLAM methods | GS backend | map reference |

---

## Related extracted notes

### Concepts
- [[../Concepts/geometry/Persistent-State-Pointmap.md]] — recurrent stateful pointmap prediction
- [[../Concepts/submap/Certified-Geometry-Memory.md]] — explicit auditable anchor memory

### Methods
- [[CUT3R-Architecture]] — persistent state + continuous pointmap pipeline
- [[Recurrent-Dense-Proposal]] — CUT3R as recurrent geometry witness

### Project
- [[Categories/Feed-Forward-3D]]


## 相关笔记

- [[geometry-model/VGGT]]
- [[geometry-model/Spann3R]]
- [[geometry-model/DUSt3R]]

## 方法继承

- **前作**：[[geometry-model/DUSt3R]], [[geometry-model/MASt3R]], [[geometry-model/Spann3R]]（连续 3D 感知 + 持续状态）
- **后继**：[[geometry-model/SLAM3R]]

## 所属分类

[[Categories/Feed-Forward-3D]]
