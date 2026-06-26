# Anchor-Structured 3DGS 论文链

## 核心思想

**用 anchor 作为 3D 空间的 compact 原型，替代 free Gaussian cloud。**

这条线的所有工作都建立在一个共同的表示上：

```
Anchor（compact 3D 几何原型） → Gaussian（renderable primitive）
```

区别在于：anchor 怎么来、Gaussian 怎么从 anchor 生成、以及这个表示用在什么场景。

---

## 论文链

```
Scaffold-GS (CVPR 2024 Highlight)
    ↓ 首次提出 anchor → neural Gaussian 表示
ContextGS (NeurIPS 2024)
    ↓ 在 Scaffold-GS 上做 anchor 压缩
SEGS-SLAM (ICCV 2025)
    ↓ 把 anchor-GS 表示引入 SLAM
AnchorSplat (CVPR 2026)
    ↓ 用 3D geometric priors 做 feed-forward anchor-GS
```

---

## 每篇定位

| 论文 | 年份 | 场景 | anchor 来源 | Gaussian 生成 | 核心贡献 |
|---|---|---|---|---|---|
| [[matching-representation/Scaffold-GS]] | CVPR 2024 | Offline NVS | SfM point cloud | MLP + view condition | 首次 anchor → neural Gaussian |
| [[mapping-reconstruction/ContextGS]] | NeurIPS 2024 | Compression | Scaffold-GS anchors | 同上 | 压缩 ~15×，autoregressive context |
| [[semantic/SEGS-SLAM]] | ICCV 2025 | SLAM | 在线 tracking | Scaffold-GS style + frequency pyramid | Structured GS 在 SLAM 中 |
| [[3dgs-slam/AnchorSplat]] | CVPR 2026 | Feed-forward NVS | 3D geometric priors | Feed-forward network + refiner | 3D-first，分辨率/视角解耦 |

---

## 共同范式

所有这四篇共享一个核心范式：

1. **anchor = compact 3D 原型** — 比 Gaussian 更少的数量，更高层的抽象
2. **Gaussian 从 anchor 派生** — 不是凭空出生，有 parent-child 关系
3. **结构化表示** — 替代 vanilla 3DGS 的 free cloud
4. **减少冗余** — 同一 3D surface 不需要上千个 Gaussian，一个 anchor 就够了

---

## 差异轴

### 场景轴
- **NVS**（Scaffold-GS, ContextGS, AnchorSplat）：offline，有完整数据集
- **SLAM**（SEGS-SLAM）：online，tracking + mapping 同时

### anchor 来源轴
- **SfM priors**（Scaffold-GS）：需要 COLMAP 预处理
- **3D geometric priors**（AnchorSplat）：sparse point clouds / voxels / RGB-D
- **在线 tracking**（SEGS-SLAM）：从 monocular/stereo/RGB-D tracking 派生

### Gaussian 生成轴
- **MLP + view condition**（Scaffold-GS, SEGS-SLAM）：学习式
- **feed-forward + refiner**（AnchorSplat）：一次性预测
- **迭代优化**（vanilla 3DGS）：每帧 densification/pruning

---

## 与 SkelGS-SLAM 的关系

你的 [[DPVO-style AnchorBank]] 设计处于这条链的 **SLAM 分支**，但比 SEGS-SLAM 更进一步：

| 维度 | SEGS-SLAM | SkelGS-SLAM |
|---|---|---|
| anchor 来源 | Scaffold-GS style | DPVO patch graph + depth reserve |
| anchor 更新 | online tracking | multi-view fusion + merge/split/suppress |
| Gaussian 归属 | Scaffold | explicit support domain `Pi_i` |
| loop correction | 无 | DPV-SLAM global BA |
| appearance 处理 | AfME embedding | `v_app` 与 `v_geo` 分离 |

**你的设计实际上融合了四条线的思想**：
1. Scaffold-GS 的 anchor → Gaussian 范式
2. ContextGS 的 anchor 分层（你的 stable/candidate/suppressed 状态机）
3. SEGS-SLAM 的 SLAM setting
4. AnchorSplat 的 3D-first 理念（你的 anchor 直接从 3D patch graph 派生）

---

## 这张图的用途

当你看到新的 anchor-GS 论文时，可以快速定位：

```
是 NVS 还是 SLAM？ → 场景轴
anchor 怎么来？ → 来源轴
Gaussian 怎么生成？ → 生成轴
```

---

## 相关笔记

- [[matching-representation/Scaffold-GS]] — 链的起点
- [[mapping-reconstruction/ContextGS]] — 压缩层
- [[semantic/SEGS-SLAM]] — SLAM 分支
- [[3dgs-slam/AnchorSplat]] — 3D-first feed-forward
- [[Anchor定义和anchor生成结构]] — 你的 anchor 设计
- [[Structured-GS-for-SLAM]] — 你的概念笔记
- [[AnchorCompression-for-SLAM]] — ContextGS 到 SLAM 的迁移
- [[AnchorLevel-ContextModel]] — 你的 anchor 压缩方法
