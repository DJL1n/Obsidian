---
tags:
  - SLAM
  - dynamic-SLAM
  - consistency-aware
  - dynamic-static-decoupled
  - Gaussian-Splatting
---
# CAD-SLAM: Consistency-Aware Dynamic SLAM with Dynamic-Static Decoupled Mapping

> arXiv:2503.01935 | 2025
> 代码：有（官方发布）

---

## 0. 一句话结论
通过一致性感知和动静解耦映射，在动态场景中实现鲁棒的 SLAM 和高质量 3D Gaussian 建图。

## 1. 核心问题
现有动态 SLAM 方法（如 DS-SLAM、DROID-SLAM 动态扩展）在动静分离上依赖运动分割 mask，mask 不准时动态区域污染静态地图，静态区域误判为动态则丢失有效几何信息。

## 2. 核心方法
CAD-SLAM 提出 **一致性感知（Consistency-Aware） + 动静解耦映射（Dynamic-Static Decoupled Mapping）**：

1. **Consistency-Aware 运动检测**：
   - 利用前后向光流一致性（forward-backward consistency）和渲染一致性（rendered-to-observed consistency）双重约束检测动态区域。
   - 不仅依赖单帧光流，还通过 Gaussian map 渲染当前帧并与实际观测比较，不一致区域标记为候选动态。
   - 引入 temporal consistency 评分：连续帧中同一区域的运动检测是否一致，过滤瞬时误检。

2. **Dynamic-Static Decoupled Mapping**：
   - **静态 map**：用 3D Gaussian Splatting 表示，仅在确认静态的区域添加和优化 Gaussian。静态 map 参与 tracking 和全局 BA。
   - **动态 map**：独立维护动态对象的 Gaussian 集合，不纳入静态 tracking。动态 Gaussian 有独立的生命周期——每帧或每 submap 刷新，不参与全局一致性优化。
   - 两个 map 共享 rendering pipeline 但分离 optimization。

3. **Tracking 机制**：相机位姿优化仅使用静态 Gaussian 的 photometric + depth 渲染，避免动态物体引入的梯度噪声。动态区域被 mask 排除。

4. **Consistency feedback loop**：tracking 结果反过来验证静态 map 的一致性——如果某区域的 Gaussian 在多个视角下产生不一致的渲染，该区域可能被重新标记为动态。

## 3. 实验结果
在 TUM-RGBD、Replica 动态场景等基准上，CAD-SLAM 在相机跟踪精度（ATE）上优于基线约 15-30%，同时动态物体分割精度和静态地图渲染质量（PSNR）均达到 SOTA。

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：
  1. **动静解耦策略**：这是 SkelGS-SLAM 最直接可借鉴的设计。SkelGS-SLAM 可以维护 static anchors（长期稳定，参与 CertifiedGeometryPacket）和 dynamic anchors（短期存在，不参与全局一致）。
  2. **一致性感知机制**：CAD-SLAM 的双重一致性检测（光流 + 渲染）可以转化为 SkelGS-SLAM 的 CertifiedGeometryPacket 验证流程——geometry proposal 必须通过多源一致性校验。
  3. **动态 Gaussian 独立生命周期**：SkelGS-SLAM 中，动态 anchor 可以被标记为 non-certified，不参与全局 map 但可用于渲染和 novelty 视图合成。
- **差异**：CAD-SLAM 使用 RGB-D depth，SkelGS-SLAM 是单目；CAD-SLAM 的 motion mask 依赖光流，SkelGS-SLAM 可基于 MASt3R/DROID 的 correspondence 一致性判断动静。
- **融合方向**：SkelGS-SLAM 的 anchor 可以引入 consistency score——每个 anchor 维护一个基于多帧几何一致性的评分，低于阈值的 anchor 被标记为 dynamic 或 uncertain，不参与 CertifiedGeometryPacket 生成。这将 CAD-SLAM 的 mask-based 方法升级为 confidence-based 的软解耦。

## 相关笔记
- [[3dgs-slam/4DGS-SLAM]] — 4D GS SLAM 动态场景处理
- [[3dgs-slam/SplaTAM]] — RGB-D SLAM with silhouette gate
- [[Dynamic-Scene-Handling]] — 动态场景处理策略对比
- [[CertifiedGeometryPacket]] — 几何数据包一致性机制

## 方法继承

- **前作**：[[dynamic-gs/DGS-SLAM]], [[slam-frontend/DROID-SLAM]], [[geometry-model/MASt3R]]（consistency-aware dynamic SLAM）
- **后继**：无

## 所属分类

[[Categories/Dynamic-GS-SLAM]]
