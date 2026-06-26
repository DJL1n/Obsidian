# Uncertainty-Aware Mapping

## 核心问题
3DGS map 中哪些 region 可靠、哪些不可靠？如何量化？

## 演进线

```
VarSplat (2026) → Primitive-Level Reliability → AnchorReliability Admission Gate
```

## 论文群

- [[3dgs-slam/VarSplat]] — per-splat appearance variance σ²，三级加权
- [[CG-SLAM]] — depth-driven geometric uncertainty（如果有的话）

## 与你的项目关联

VarSplat 的 primitive-level reliability 直接转化成了你的 [[AnchorReliability-Admission-Gate]]：
- 每个 anchor 存 reliability score
- temporal repeatability + depth scale consistency + normal stability
- 决定 anchor 是否晋升为成熟状态

## 相关 Concepts

- [[Primitive-Level-Reliability]] — 每个 primitive 带 reliability state
- [[Appearance-vs-Geometry-Uncertainty]] — 外观 vs 几何不确定性的区分
- [[AnchorReliability-Admission-Gate]] — 你的实现

## 相关线程

- [[anchor-structured-gs]] — anchor reliability 是表示层的一部分
- [[dynamic-scene-handling]] — 不确定性是动态检测的信号之一
