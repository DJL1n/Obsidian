---
tags:
  - 3D-Perception
  - continuous-representation
  - persistent-state
  - foundation-model
  - spatial-understanding
---
# Continuous 3D Perception Model with Persistent State

> arXiv:2505.xxxx | 2025
> 代码：待发布（截至笔记编写时）

---

## 0. 一句话结论
用持续状态（persistent state）构建连续 3D 感知模型，实现跨帧/跨会话的增量式 3D 理解。

## 1. 核心问题
现有 3D 视觉基础模型（如 MASt3R、[[geometry-priors/feed-forward/DUSt3R]]）以独立图像对或固定窗口处理场景，无法维持长期状态；SLAM 系统虽然维护状态但缺乏语义理解能力，两者之间缺少统一的 3D 感知范式。

## 2. 核心方法
该论文提出一种 **Continuous 3D Perception 框架**，核心创新在于 persistent state 机制：

1. **Persistent State 表示**：模型维护一个长期持久的 3D 场景状态（类似 SLAM 的地图，但表示形式更灵活）。该状态可以是：
   - Implicit neural representation（如 NeRF-style MLP）
   - Explicit point cloud + feature descriptors
   - 或 hybrid 表示（sparse tokens + dense features）
   状态在每次新观测到来时增量更新，而非从头重建。

2. **Incremental Update Mechanism**：新图像到达时，模型执行：
   - **State retrieval**：从 persistent state 中检索与当前观测相关的上下文（类似 memory mechanism）。
   - **Cross-view association**：将当前观测与历史状态中的对应区域关联，利用基础模型的 dense matching 能力。
   - **State refinement**：根据新观测更新 3D 状态，包括几何（位置/深度）和语义（类别/属性）信息。

3. **Continuous 3D Prediction**：基于 persistent state，模型可以：
   - 预测任意视角的渲染（novel view synthesis）
   - 进行 3D 语义分割/实例分割
   - 估计场景级 3D 结构（如房间布局、物体关系）
   这些预测在状态更新后自动改进，无需重新训练。

4. **Temporal Consistency**：持久状态本身充当时间一致性正则化——新观测必须与已有状态兼容，避免帧间抖动和累积漂移。

## 3. 实验结果
在 ScanNet、nuScenes 等基准上，该模型在 3D 感知任务（分割、匹配、渲染）上相比非 persistent 方法有显著提升，特别是在跨会话场景理解上——模型能记住之前见过的场景并增量完善。

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：
  1. **Persistent state 概念**：SkelGS-SLAM 的 submap/anchor map 本质上就是一个 persistent state。关键启示是——persistent state 不仅是几何存储，还承载语义理解和跨视图关联能力。
  2. **Incremental update 范式**：新帧到来时不重建，只更新。这与 SkelGS-SLAM 的设计哲学完全一致——CertifiedGeometryPacket 是增量更新单元。
  3. **State retrieval as context**：从持久状态中检索相关上下文进行当前帧处理，可以类比 SkelGS-SLAM 中从 submap 检索相关 anchors 作为 geometry proposal 的先验。
  4. **Temporal consistency 正则化**：persistent state 对漂移的自然抑制，验证了 SkelGS-SLAM 中 submap-level 一致性校验的重要性。
- **差异**：该模型是通用 3D 感知系统，非 SLAM；无显式位姿估计回路。SkelGS-SLAM 需要 [[slam-frontends/patch-based/DPVO]] 前端提供实时 pose。
- **融合方向**：SkelGS-SLAM 的 persistent state（submap）可以扩展为 multimodal state——不仅存储几何（anchor positions, Gaussian attributes），还存储 semantic priors（来自基础模型的 scene understanding）。这将 SkelGS-SLAM 从纯几何 SLAM 推向 perception-level 系统。具体实现上，可以用 MASt3R 或类似基础模型作为 persistent state 的 semantic update module，在 CertifiedGeometryPacket 中附加语义标签。

## 相关笔记
- [[[[geometry-priors/feed-forward/MASt3R]]]] — 3D 匹配基础模型
- [[[[gs-slam/experimental/AMB3R]]]] — feed-forward + backend 重建
- [[[[gs-slam/structured/AnchorSplat]]]] — anchor-structured Gaussian
- [[Persistent-State-Design]] — 持续状态设计原则
