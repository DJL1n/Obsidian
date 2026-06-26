# AnchorSplat: Feed-Forward 3D Gaussian Splatting with 3D Geometric Priors

> arXiv:2604.07053 | CVPR 2026
> 作者：Xiaoxue Zhang, Xiaoxu Zheng, Yixuan Yin, Tiao Zhao, Kaihua Tang, Michael Bi Mi, Zhan Xu, Dave Zhenyu Chen
> GitHub: [Zhang-Xiaoxue/AnchorSplat](https://github.com/Zhang-Xiaoxue/AnchorSplat) (7 stars, 代码未开放)
> ---

## 0. 一句话结论

AnchorSplat 提出用 **3D geometric priors（稀疏点云 / voxels / RGB-D 点云）作为 anchor**，构建 **anchor-aligned Gaussian representation**，实现 feed-forward 3DGS 重建。**关键创新不是 anchor 本身，而是把 anchor 从"像素对齐"解放到"3D 空间对齐"**，使 Gaussian 数量、分辨率、视角数都解耦。

---

## 1. 核心问题

现有 feed-forward 3DGS 方法（如 ZeroSplat、InstantSplat）采用 **pixel-aligned 公式**：每个 2D 像素映射一个 3D Gaussian。这导致：

1. **Gaussian 数量与输入图像分辨率强耦合** — 高分辨率 = 爆炸式增长
2. **Gaussian 表示与输入图像紧密绑定** — 无法独立于视角数量
3. **计算效率低** — 大量冗余 Gaussian 解释同一 3D 几何
4. **重建保真度受限** — 像素级映射无法捕获场景级几何结构

---

## 2. 核心方法

### 2.1 Anchor-aligned Gaussian Representation

```
3D Geometric Priors（稀疏点云 / voxels / RGB-D 点云）
    ↓
Anchors（3D 空间中的 compact 几何原型）
    ↓
Feed-Forward Network（预测每个 anchor 的 Gaussian parameters）
    ↓
Gaussian Splatting Render
```

与 pixel-aligned 方法的区别：

| 维度 | Pixel-aligned（现有） | Anchor-aligned（AnchorSplat） |
|---|---|---|
| 输入 | 2D 图像像素 | 3D 几何先验 |
| Gaussian 数量 | 与分辨率绑定 | 与 anchor 数量绑定 |
| 视角依赖 | 强 | 弱 |
| 空间表示 | 2D→3D 反投影 | 直接 3D 空间 |

### 2.2 3D Geometric Priors 类型

支持三种 prior：
- **Sparse point clouds**（如 COLMAP/MASt3R sparse features）
- **Voxels**（体素网格）
- **RGB-D point clouds**（深度图反投影）

这些 priors 提供 3D 空间的"骨架"，anchor 挂在骨架上，而不是挂在像素上。

### 2.3 Gaussian Refiner

中间 Gaussian 经过 feed-forward 预测后，用 **Gaussian Refiner** 微调：
- 仅需少量 forward passes（不迭代优化）
- 调整位置、协方差、不透明度、颜色
- 提升重建保真度

---

## 3. 实验结果

- **基准**：ScanNet++ v2 NVS（Novel View Synthesis）
- **结论**：SOTA performance
- **关键指标**：
  - 比现有方法 **Gaussian 数量大幅减少**
  - **view consistency 更强**（跨视角更一致）
  - 重建保真度更高

---

## 4. 与你 SkelGS-SLAM 的关联

### 4.1 直接支持你的判断

你的笔记中多次提到：

> "anchor 可以是稀疏的" — AnchorSplat 明确用 sparse point clouds / voxels / RGB-D point clouds 作为 anchor-aligned Gaussian prior

这与你的 **AnchorBank 设计**高度一致：
- 你的 anchor = compact 3D geometry unit，融合 DPVO patch trajectories
- AnchorSplat 的 anchor = 3D geometric prior 驱动的 Gaussian representation

### 4.2 可借鉴点

1. **3D-first 而非 2D-first**
   - AnchorSplat 直接从 3D 空间构建表示
   - 你的 DPV-SLAM patch graph → AnchorBank 也是 3D-first
   - 这比先做 2D 匹配再反投影更干净

2. **anchor 作为 Gaussian 的"出生地"**
   - AnchorSplat：每个 anchor 预测一组 Gaussian parameters
   - 你的设计：stable anchor 的 support domain `Pi_i` 控制 Gaussian 归属
   - 两者都认为 **Gaussian 应该从 anchor 出生，而不是凭空出现**

3. **feed-forward 预测替代迭代优化**
   - AnchorSplat 用 feed-forward network + Gaussian Refiner
   - 这对你的 **ChildGS / MLP Gaussian predictor** 方向有启发：可以用类似思路预测 Gaussian parameters

4. **与 ContextGS 的关系**
   - ContextGS：anchor-level context model 做压缩
   - AnchorSplat：anchor-aligned representation 做重建
   - 两者证明 **anchor 是 3DGS 的 compact 表示单位**，不是 Scaffold-GS 独有的

### 4.3 差异点

| 维度 | AnchorSplat | SkelGS-SLAM |
|---|---|---|
| 场景 | 静态室内（ScanNet） | 动态/半动态（SLAM） |
| anchor 来源 | 3D geometric priors（一次性） | DPVO/DPV-SLAM patch graph（持续更新） |
| Gaussian 更新 | feed-forward + refiner（一次性） | online birth/prune/update（持续） |
| tracking | 无 | 有（DPVO frontend） |
| loop closure | 无 | 有（DPV-SLAM） |

**AnchorSplat 是 feed-forward 重建，不是 SLAM。** 但它的核心思想（3D anchor-aligned Gaussian）可以直接迁移到你的 online SLAM pipeline。

---

## 5. 对你的启示

1. **sparse anchor 路线是对的** — AnchorSplat 证明 sparse point clouds 作为 anchor 够用
2. **Gaussian 从 anchor 出生** — 你的 `spawn_budget_hint` 和 `support_domain Pi_i` 设计方向正确
3. **feed-forward 预测可以替代部分迭代优化** — ChildGS / MLP Gaussian predictor 值得进一步探索
4. **你的 DPV-SLAM AnchorBank 比 AnchorSplat 更强** — 因为你有持续更新、loop closure、多视图验证，而 AnchorSplat 是一次性快照

---

## 6. 局限

1. **不是在线方法** — 不处理 tracking/pose/depth/loop/birth
2. **依赖 3D geometric priors** — 没有 good priors 时效果未知
3. **代码未开放** — GitHub 只有 README，没有源码（7 stars，0 forks）
4. **仅室内静态场景** — 未测试室外/动态/大尺度

---

## 相关笔记

- [[[[gs-slam/structured/Scaffold-GS]]]] — anchor → Gaussian 表示的起点
- [[[[gs-slam/structured/ContextGS]]]] — anchor 压缩
- [[[[gs-slam/structured/SEGS-SLAM]]]] — SLAM 分支
- [[Anchor定义和anchor生成结构]] — 你的 anchor 设计
- [[ChildGS]] — Gaussian 从 anchor 出生
- [[[[gs-slam/monocular/DPV-SLAM]]]] — 你的前端 base

## 方法继承

- **前作**：[[gs-slam/structured/Scaffold-GS]], [[gs-slam/structured/ContextGS]], [[gs-slam/structured/SEGS-SLAM]], [[geometry-priors/feed-forward/MASt3R]], [[slam-frontends/patch-based/DPVO]], [[gs-slam/monocular/DPV-SLAM]]（3D-first feed-forward anchor GS）
- **后继**：无
