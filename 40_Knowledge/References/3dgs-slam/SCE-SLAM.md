# SCE-SLAM: Scale-Consistent Monocular SLAM via Scene Coordinate Embeddings

> arXiv:2311.03644 | 2023 (ECCV 2024)
> 代码：有（[GitHub](https://github.com/ExMLab/SCE-SLAM)）

## 0. 一句话结论
用场景坐标回归网络解单目尺度模糊，实现 meter-level 尺度一致的 SLAM 轨迹。

## 1. 核心问题
单目 SLAM 固有的尺度模糊（scale ambiguity）意味着重建的轨迹和地图只能保证相似形，无法获知真实物理尺度。这对需要精确尺度信息的应用（机器人导航、AR 测量、与地图融合）是根本性障碍。SCE-SLAM 问：能否通过学习场景坐标 embedding（scene coordinate regression），在单目 SLAM 框架内直接估计物理尺度？

## 2. 核心方法
SCE-SLAM 将**场景坐标回归**（Scene Coordinate Embedding, SCE）与 SLAM 管线融合：
1. **SCE Network**：一个深度学习网络，对输入图像的每个像素回归其在世界坐标系中的 3D 坐标。该网络在训练阶段使用真实尺度数据（如 ScanNet 的 RGB-D 标注）学习像素到世界坐标的映射。
2. **SCE-Driven Pose Estimation**：新帧到来时，SCE 网络输出每像素的 3D 坐标估计。这些坐标与前一帧的 SCE 预测之间建立 PnP 约束（将 SCE 输出的 3D 点视为"已知的世界坐标"），通过 PnP + RANSAC 求解帧间位姿。
3. **Scale Anchor**：SCE 网络输出的坐标是绝对世界坐标（带真实尺度），因此帧间位姿估计天然具有正确的物理尺度。SLAM 的尺度模糊被 SCE 的绝对尺度锚定消除。
4. **SLAM Integration**：SCE 约束与传统 VO/BA 结合——VO 提供初始位姿，SCE 提供尺度锚定，BA 联合优化。

Pipeline：输入帧 → SCE 网络（每像素 3D 坐标）→ PnP 位姿估计 → 与 VO 融合 → BA 优化 → 输出尺度一致轨迹。

## 3. 实验结果
- ScanNet 上尺度恢复精度达到 ~0.1m 级别（比传统单目方法提升 5-10 倍）
- 轨迹精度（ATE）与 RGB-D SLAM 相当，远优于无尺度信息的单目 SLAM
- 在已知尺度训练数据分布的场景上效果最佳（室内场景）
- 局限：SCE 网络需要训练数据，泛化到新场景（尤其是未见过尺度的场景）性能下降

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：SCE 网络提供的前端尺度锚定可直接替代 SkelGS-SLAM 中的尺度初始化模块。SCE 输出的 3D 坐标还可以直接用于 anchor 的初始位置估计——不再需要从 SFM 推断
- **差异**：SCE 依赖训练数据，SkelGS-SLAM 的 anchor 结构是 unsupervised 从几何自生成的；SCE 不做 GS mapping，只做位姿
- **融合方向**：将 SCE 作为 SkelGS-SLAM 的尺度模块——用 SCE 输出的世界坐标做 anchor 的初始定位，用 DPVO/DSO 做帧间追踪 refinement。SCE + DPVO 联合前端可同时解决尺度模糊和纹理缺失问题，是 SkelGS-SLAM 单目模式的理想前端

## 相关笔记
- [[3dgs-slam/SCE-SLAM]] 是"学习型 SLAM"的代表作，将监督学习的尺度先验引入单目 SLAM
- 与 [[geometry-model/MASt3R]] 的 scene coordinate 方法同源，但更轻量
- 对 SkelGS-SLAM 的尺度问题有直接解决价值

## 方法继承

- **前作**：[[geometry-model/MASt3R]], [[DSO]]（scene coordinate embeddings）
- **后继**：无

## 所属分类

[[Categories/Geometric-Foundation-Models]]
