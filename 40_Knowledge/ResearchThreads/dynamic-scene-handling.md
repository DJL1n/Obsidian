# Dynamic Scene Handling in SLAM

## 核心问题
如何处理 SLAM 中的动态物体？区分静态背景和运动前景。

## 演进线

```
WildGS-SLAM → DGS-SLAM → ADD-SLAM/CAD-SLAM → Dy3DGS-SLAM
```

## 论文群

### 动态 GS SLAM
- [[dynamic-gs/WildGS-SLAM]] — DINOv2 features 过滤动态
- [[dynamic-gs/DGS-SLAM]] — dynamic Gaussian SLAM
- [[dynamic-gs/ADD-SLAM]] — adaptive dynamic dense SLAM
- [[dynamic-gs/Dy3DGS-SLAM]] — monocular dynamic SLAM

### 动态检测
- [[dynamic-gs/UP-SLAM]] — probabilistic approach
- [[dynamic-gs/EAG3R]] — event-augmented dynamic handling
- [[dynamic-gs/MonST3R]] — dynamic video geometry

### 你的设计
- [[Anchor定义和anchor生成结构]] — dynamic risk 作为 anchor state 的一部分
- [[Motion-Geometry-Coherence-Loss]] — 运动几何一致性检测动态

## 与你的项目关联

你的 SkelGS-SLAM 的核心卖点就是"dynamic-aware"。anchor 设计中的 `dynamic_risk` 字段就是直接对应这个方向。

## 相关 Concepts

- [[dynamic]] tag 的 concepts
- [[anchor/Submap-Level-Provenance]] — 子图溯源用于动态检测
- [[tracking/Motion-Geometry-Coherence-Loss]] — 运动一致性

## 相关线程

- [[uncertainty-aware-mapping]] — 动态区域天然不可靠
- [[feed-forward-3dgs]] — 3D prior 可用于运动一致性验证
