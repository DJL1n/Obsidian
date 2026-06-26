# Gaussian-LIC 系列：LiDAR-Inertial-Camera Gaussian Splatting SLAM

> ## Gaussian-LIC (前作)
> arXiv:2404.06926 | ICRA 2025
> 作者：Xiaolei Lang, Laijian Li, Chenming Wu, Chen Zhao, Lina Liu, Yong Liu, Jiajun Lv, Xingxing Zuo
> 代码：已开源
>
> ## Gaussian-LIC2 (续作)
> arXiv:2507.04004 | 2025-07 submitted (论文写 "code will be made publicly available")
> 作者：Xiaolei Lang, Jiajun Lv, Kai Tang, Laijian Li, Jianxin Huang, Lina Liu, Yong Liu, Xingxing Zuo
> 代码：**已开源** — [APRIL-ZJU/Gaussian-LIC](https://github.com/APRIL-ZJU/Gaussian-LIC) (558 stars, 2026-02-21 commit 从 LIC 升级到 LIC2, `src/` 目录含代码)
> 另一个 repo [XingxingZuo/gaussian_lic2](https://github.com/XingxingZuo/gaussian_lic2) 只是 project page (21 stars)
> ---

## 0. 一句话结论

**Gaussian-LIC**：实时 photo-realistic SLAM，LiDAR+IMU+Camera 三传感器融合，C++/CUDA 纯手写，代码已开源。

**Gaussian-LIC2**：续作，增加 continuous-time 轨迹优化、zero-shot depth 填补 LiDAR 盲区、Gaussian map 反哺里程计、CUDA 加速策略。

---

## 1. 核心问题

GS-SLAM 系统要么用 camera（单目/stereo/RGB-D），要么用 LiDAR。**三传感器融合的 Gaussian SLAM 几乎空白**：

- 纯 camera GS-SLAM → 没有绝对深度先验，尺度漂移
- 纯 LiDAR SLAM → 没有 photorealistic rendering
- LiDAR+camera fusion（offline）→ 需要预知 poses，不是 SLAM

---

## 2. Gaussian-LIC（前作，ICRA 2025）

### 系统

```
LiDAR + IMU + Camera
    ↓
LiDAR-inertial-camera SLAM（位姿估计）
    ↓
LiDAR points + visual triangulated points → Gaussian initialization
    ↓
Online Gaussian optimization（C++/CUDA）
    ↓
Photo-realistic rendering + sky modeling + varying exposure
```

### 核心创新

1. **三传感器融合** — LiDAR（几何）+ IMU（高频运动）+ Camera（外观）
2. **Visual points 填补 LiDAR 盲区** — 三角化视觉点 + LiDAR 点共同初始化 Gaussian
3. **纯 C++/CUDA 实现** — 不是 PyTorch，全部手写加速策略
4. **天空建模 + 曝光变化** — 高质量渲染
5. **估计位姿下渲染质量超越 ground-truth 位姿**

### 代码状态

**已开源**（与 LIC2 不同）。

---

## 3. Gaussian-LIC2（续作，2025）

### 新增能力

在 LIC 基础上增加了：

1. **Continuous-time trajectory optimization**
   - IMU pre-integration factor
   - LiDAR point factor
   - Camera photometric factor（从 Gaussian map）
   - Loop closure factor

2. **Zero-shot depth model**
   - 轻量级 zero-shot depth
   - 结合 RGB appearance + sparse LiDAR
   - 在 LiDAR-blind 区域生成 dense depth
   - 可靠 Gaussian initialization

3. **LiDAR 监督 Gaussian 优化**
   - sparse LiDAR depths → 精确几何监督
   - CUDA 加速优化

4. **Gaussian map 反哺里程计**
   - photometric constraints from Gaussian map → factor graph
   - LiDAR 退化场景下改进位姿估计

5. **自建数据集**
   - LiDAR-Inertial-Camera 数据集
   - ground-truth poses + depth maps + extrapolated trajectories
   - 评估 out-of-sequence novel view synthesis

### 代码状态

**未开源**。GitHub 只有 project page（21 stars），写着 "Coming_soon"。

---

## 4. 两篇对比

| 维度 | Gaussian-LIC | Gaussian-LIC2 |
|---|---|---|
| 年份 | 2024 (ICRA 2025) | 2025 |
| 传感器 | LiDAR+IMU+Camera | LiDAR+IMU+Camera |
| Gaussian init | visual points + LiDAR | zero-shot depth + LiDAR |
| 位姿优化 | 标准 factor graph | continuous-time |
| LiDAR 监督 | 初始化 | 持续监督优化 |
| Gaussian→odometry | 无 | 有（photometric feedback） |
| 代码 | ✅ 开源 | ✅ 同一个 repo 已升级成 LIC2 |

---

## 5. 与你 SkelGS-SLAM 的关联

### 5.1 可借鉴

**depth prior 的多层权重**：

```
LiDAR points        → 最高权重（精确几何）
visual triangulated → 中等权重（tracked）
zero-shot depth     → 低权重（dense 但不精确）
```

这直接映射到你的 `DepthReservePool` 设计：不同来源的 depth prior 应该有不同的 reserve weight。

**LiDAR 监督 → anchor `v_geo`**：

- LiDAR point 落入 anchor support domain → 降低 `v_geo`
- 无 LiDAR 覆盖 → `v_geo` 不变，继续靠 DPVO 约束

### 5.2 差异

| 维度 | LIC/LIC2 | SkelGS-SLAM |
|---|---|---|
| 传感器 | LiDAR+IMU+Camera | 单目 |
| 表示 | free Gaussian cloud | anchor-structured GS |
| 前端 | LiDAR-inertial SLAM | DPVO patch graph |
| 代码 | LIC 开源, LIC2 未开源 | — |

**LIC/LIC2 是 free Gaussian cloud，不是 anchor-structured。这是关键区别。**

### 5.3 融合方向

1. 从 `APRIL-ZJU/Gaussian-LIC` 开源代码学 LiDAR+CUDA 加速策略（同一个 repo 已升级到 LIC2）
2. 用 LIC2 的 zero-shot depth + continuous-time 优化思路
3. 迁移到你的 anchor bank：LiDAR 监督降低 `v_geo`，zero-shot depth 进 reserve pool

---

## 6. 在 LiDAR-GS 谱系中的位置

```
GS-SDF (offline, LiDAR+SDF, 不是 SLAM)
    ↓
Gaussian-LIC (ICRA 2025, online SLAM, free GS, 代码开源)
    ↓
Gaussian-LIC2 (2025, online SLAM + continuous-time + zero-shot depth, ✅ 代码开源)
```

---

## 相关笔记

- [[mapping/sdf-based/GS-SDF]] — LiDAR+SDF 的 offline 方法
- [[Anchor定义和anchor生成结构]] — 你的 anchor 设计
- [[AnchorStructured-GS-Paper-Chain]] — anchor-GS 论文链

## 方法继承

- **前作**：[[slam-frontends/patch-based/DPVO]], [[gs-slam/monocular/GS-SLAM]]（LiDAR-IMU-Camera GS-SLAM）
- **后继**：无
