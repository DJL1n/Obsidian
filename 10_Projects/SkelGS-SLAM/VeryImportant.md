# SkelGS-SLAM — Very Important Notes

> 本文件记录 SkelGS-SLAM 项目的核心思路、设计决策、关键文献关联。
> 其他临时文件可以随意增删，这里的东西不要丢。

---

## 项目定位

SkelGS-SLAM：基于 3D Gaussian Splatting 的 SLAM 系统，核心创新在于 **anchor-based 的 sparse structure + GS mapping**。

关键问题：
- 如何用少量 anchor points 表达场景几何结构
- 如何在 online 模式下维持 anchor 的 consistency
- 如何把 anchor 作为 GS primitive 的 scaffold，实现高效 mapping

---

## 核心思路

<!-- 在此记录关键设计思路、假设、待验证的点 -->

---

## 关键文献关联

### GS-SLAM 系统（直接竞品/基线）
- [[40_Knowledge/References/3dgs-slam/GigaSLAM]] — 分层 Gaussian splat 的大尺度单目 SLAM
- [[40_Knowledge/References/3dgs-slam/SplaTAM]] — RGB-D Splatting-based Tracking and Mapping
- [[40_Knowledge/References/3dgs-slam/VarSplat]] — Uncertainty-aware GS for RGB-D SLAM
- [[40_Knowledge/References/3dgs-slam/AnchorSplat]] — Feed-Forward GS with 3D geometric priors（anchor 概念来源）
- [[40_Knowledge/References/3dgs-slam/Splat-SLAM]]
- [[40_Knowledge/References/3dgs-slam/GS-SLAM]]
- [[40_Knowledge/References/3dgs-slam/GauS-SLAM]]
- [[40_Knowledge/References/3dgs-slam/MonoGS]]
- [[40_Knowledge/References/3dgs-slam/OpenMonoGS-SLAM]]
- [[40_Knowledge/References/3dgs-slam/SING3R-SLAM]] — Submap-based，与 anchor 设计相关

### SLAM 前端
- [[40_Knowledge/References/slam-frontend/DROID-SLAM]] — 深度估计 + visual odometry
- [[40_Knowledge/References/slam-frontend/DPVO]]
- [[40_Knowledge/References/slam-frontend/MASt3R-SLAM]]

### 几何基础模型
- [[40_Knowledge/References/geometry-model/MASt3R]] — 3D 几何 prior 的来源
- [[40_Knowledge/References/geometry-model/DUSt3R]]
- [[40_Knowledge/References/geometry-model/VGGT]]
- [[40_Knowledge/References/geometry-model/CUT3R]]
- [[40_Knowledge/References/geometry-model/Spann3R]]

### GS Mapping / Densification
- [[40_Knowledge/References/mapping-reconstruction/resgs]] — Residual densification
- [[40_Knowledge/References/mapping-reconstruction/absgs]] — Gradient collision / fine detail recovery
- [[40_Knowledge/References/mapping-reconstruction/fastgs]] — 加速 training
- [[40_Knowledge/References/mapping-reconstruction/GSFusion]]
- [[40_Knowledge/References/mapping-reconstruction/GS-SDF]]

### Survey / 分类
- [[40_Knowledge/References/survey/CoGS-SLAM-Survey]] — Collaborative GS-SLAM 综述
- [[40_Knowledge/References/Categories/4D-Gaussian]]
- [[40_Knowledge/References/Categories/Structured-GS]]

---

## 待验证假设

<!-- 记录需要实验验证的假设，验证后打勾或移入决策区 -->

---

## 决策记录

<!-- 重要设计决策：为什么选 A 不选 B -->

---

## 实验笔记

<!-- 记录实验设计、结果、分析 -->

---

## 问题清单

<!-- 当前 blocker、待解决的 technical debt -->

---

## 近期计划

<!-- 下一步要做什么 -->
