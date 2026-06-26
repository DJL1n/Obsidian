# DPVO: Deep Patch Visual Odometry

> arXiv:2108.06762 → 2205.09652 (Camera Ready) | 2022
> 代码：有（[GitHub](https://github.com/hkust-vic/dpvo)）

## 0. 一句话结论
以深度学习替代传统 VO 前端，patch-based 光流估计在 KITTI 上 SOTA。

## 1. 核心问题
传统基于特征的 VO 前端（如 ORB-SVO）在纹理缺失、动态场景、剧烈运动下鲁棒性差，且 hand-crafted 特征提取器无法端到端优化。DPVO 问：能否用一个纯学习驱动的 patch-wise 光流估计器来做稠密视觉里程计，且实时运行？

## 2. 核心方法
DPVO 完全抛弃了特征点匹配范式。Pipeline 如下：
1. **Pose-Adaptive Feature Network**：从两帧图像中提取 patch-level 特征，将当前帧的 pose 假值注入特征网络，使特征对位姿敏感。
2. **Multi-Scale Warping**：将参考帧特征按当前帧位姿估计 warp 到当前帧，在不同尺度上对比 warped 与当前特征。
3. **Flow/Residual Refinement**：通过学习的光流（残差）补偿 warp 误差，逐层 refine 位姿。
4. **Gradient-Only Pose Optimization**：与传统 VO 不同，DPVO 的位姿优化不是基于稀疏特征点的 BA，而是基于所有 patch 的 photometric error 梯度直接更新 SE(3) 位姿，实现 end-to-end 可微。

DPVO 用 sliding window 做 VO（无全局 BA），每帧参考帧数可配置。训练用合成数据（Blender）加真实数据微调。

## 3. 实验结果
- KITTI VO 序列上 ATE RMSE 显著优于 L-SVO、SVO2、VIO 方法（~2-3x 改进）
- 实时运行（TUM-Freiburg 上 30+ FPS）
- 在纹理缺失和动态物体区域比特征点方法更鲁棒
- 但作为 VO（无回环检测），长期漂移无法避免

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：patch-based 光流估计可直接作为 SkelGS-SLAM 的初始位姿估计，替代传统特征匹配。Pose-Adaptive 特征网络对 anchor-based GS 注册同样有效
- **差异**：DPVO 是 VO 而非 SLAM，无地图构建/回环；SkelGS-SLAM 需要全局一致性和稀疏/半稠密地图
- **融合方向**：用 DPVO 做 SkelGS-SLAM 的帧间追踪前端，利用其端对端可微性和对纹理缺失区域的鲁棒性，同时 SkelGS 的 anchor 结构为 DPVO 提供几何约束，形成互补

## 相关笔记
- [[slam-frontend/DPVO]] 后续被 [[3dgs-slam/DPV-SLAM]] 扩展到 SLAM（加入了回环检测和全局 BA）
- [[slam-frontend/DPVO]] 的 patch 分辨率、尺度选择对精度影响大，需根据场景调整

## 方法继承

- **前作**：无（开创性工作）（patch-based VO）
- **后继**：[[3dgs-slam/DPV-SLAM]]

## 所属分类

[[Categories/SLAM-Frontends]]
