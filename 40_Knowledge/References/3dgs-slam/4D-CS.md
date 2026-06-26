---
tags:
  - LiDAR
  - 4D-CS
  - semantic-segmentation
  - spatio-temporal
  - clustering
---
# 4D-CS: Exploiting Cluster Prior for 4D Spatio-Temporal LiDAR Semantic Segmentation

> arXiv:2310.00621 | 2023 (NeurIPS 2023)
> 代码：有（官方实现）

---

## 0. 一句话结论
利用 cluster prior 挖掘 4D（空间+时间）LiDAR 点云的时空一致性，显著提升语义分割精度。

## 1. 核心问题
现有 4D LiDAR 语义分割方法通常独立处理每帧，或利用简单的时序融合策略，未能充分利用点云在时空域中的聚类结构先验，导致动态物体和小物体分割精度不足。

## 2. 核心方法
该论文提出 **4D Cluster-wise Selection (4D-CS)** 框架，核心在于显式建模和利用 cluster prior：

1. **Cluster Prior 构建**：首先通过几何聚类（DBSCAN 或欧式聚类）将每帧 LiDAR 点云分割为若干 object-level clusters。这些 cluster 代表语义上可能同质的点集（同一物体表面的点被聚在一起）。

2. **时空 cluster 关联**：在连续帧之间跟踪 cluster，形成 4D spatio-temporal clusters。利用 IoU matching 或运动一致性将不同帧的 cluster 配对，构建跨帧的 cluster trajectory。

3. **Cluster-level feature aggregation**：不是对单个点做特征聚合，而是在 cluster 内部进行时序特征融合。使用 attention 或 LSTM 对 cluster 级别的多帧特征进行时序建模，然后传播回每个点。

4. **Cluster-aware loss**：在训练损失中加入 cluster consistency 约束——同一 cluster 内的点在多帧间应保持语义一致性，减少帧间抖动。

5. **两阶段推理**：先进行 cluster 检测和关联，再在 cluster 轨迹上进行精细语义分类，最后将结果细化到点级别。

## 3. 实验结果
在 nuScenes 和 SemanticKITTI 数据集上，4D-CS 在 mIoU 上相比 baselines 提升约 1-3 个百分点，在动态小物体类别上增益尤为明显。

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：
  1. **Cluster prior 思想**：SkelGS-SLAM 的 anchor 本质上也是一种 cluster——一组 Gaussian 共享空间结构和运动先验。可以借鉴 cluster-level feature aggregation 的思路，在 anchor 级别而非 Gaussian 级别做时序融合。
  2. **时空一致性约束**：cluster consistency loss 可转化为 SkelGS-SLAM 中 anchor 级别的几何一致性校验——同一 anchor 下的 Gaussian 在连续帧中应满足运动学约束。
  3. **Cluster trajectory 跟踪**：可类比 SkelGS-SLAM 中 anchor 生命周期管理——创建、跟踪、淘汰。
- **差异**：4D-CS 是 LiDAR 语义分割任务，SkelGS-SLAM 是单目稠密 SLAM，输入/输出/任务完全不同。4D-CS 不涉及 pose estimation 或 photometric rendering。
- **融合方向**：SkelGS-SLAM 的 anchor 可以加入 cluster-level motion prior——不是纯几何结构约束，而是从相邻帧的 anchor 关联中学习运动先验，辅助 CertifiedGeometryPacket 的生成和置信度评分。

## 相关笔记
- [[[[gs-slam/dynamic/4DGS-SLAM]]]] — 4D GS SLAM
- [[Semantic-SLAM]] — 语义 SLAM 方法
- [[[[gs-slam/structured/AnchorSplat]]]] — anchor-structured Gaussian
- [[Cluster-Level-Feature-Aggregation]] — 聚类级特征聚合方法

## 所属分类

[[Categories/4D-Gaussian]]
