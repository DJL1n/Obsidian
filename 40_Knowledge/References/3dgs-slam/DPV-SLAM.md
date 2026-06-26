# DPV-SLAM: Deep Patch Visual SLAM

> arXiv:2208.00185 | 2022
> 代码：有（[GitHub](https://github.com/hkust-vic/dpv-slam)）

## 0. 一句话结论
将 DPVO 从 VO 升级为完整 SLAM，加入回环检测与全局 BA，端到端优化。

## 1. 核心问题
DPVO 虽然实现了高精度的 patch-based 视觉里程计，但缺乏全局一致性优化——没有回环检测和全局位姿图优化，长期运行漂移严重。DPV-SLAM 问：能否在 DPVO 的 patch-based 框架内构建完整的 SLAM 管线，且保持端到端可微？

## 2. 核心方法
DPV-SLAM 在 DPVO 基础上扩展了三个模块：
1. **回环检测**：使用 NetVLAD 提取全局视觉描述子，通过 Hamming 距离匹配历史帧，检测回环。
2. **全局 Pose Graph Optimization**：将 VO 流产生的帧间位姿约束与回环约束联合构建因子图，进行全局 BA。
3. **稠密映射（可选）**：通过 volumetric TSDF 融合或 RGB-D 稠密融合生成稠密地图。

与传统 SLAM 不同，DPV-SLAM 的 pose graph 边权重和约束由深度学习模块直接产生——patch-based photometric error 作为因子图的观测模型，而非传统的手动设计的重投影误差。

Pipeline：输入帧 → DPVO 前端（patch 匹配 + 位姿估计）→ 回环检测 → 全局 BA → 输出轨迹 + 稠密地图。

## 3. 实验结果
- TUM RGB-D 上绝对轨迹误差优于 DSO、SVO2、L-SVO 等传统方法
- KITTI 上回环检测 recall 和轨迹精度均有提升
- 端到端训练使其在纹理缺失区域表现更优
- 但全局 BA 开销较大，实时性不如纯 VO 模式

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：DPV-SLAM 展示了如何将 DPVO 前端与回环 + BA 结合，SkelGS-SLAM 可以直接复用这一架构，将 GS mapping 模块插入
- **差异**：DPV-SLAM 的稠密映射用 TSDF，SkelGS-SLAM 用 Gaussian Splatting；DPV-SLAM 无动态场景处理能力
- **融合方向**：用 DPV-SLAM 的 pose graph 框架做 SkelGS-SLAM 的追踪 + 回环层，GS mapping 作为独立模块并行运行，anchor 结构提供 GS 与 pose graph 之间的几何约束

## 相关笔记
- [[slam-frontend/DPVO]] → [[3dgs-slam/DPV-SLAM]] 是端到端 SLAM 的重要里程碑
- 与 [[3dgs-slam/GS-SLAM]] 系列的关键差异：没有用 GS 做建图，而是用传统 volumetric 方法

## 方法继承

- **前作**：[[slam-frontend/DPVO]]（DPVO → SLAM）
- **后继**：无

## 所属分类

[[Categories/Monocular-GS-SLAM]]
