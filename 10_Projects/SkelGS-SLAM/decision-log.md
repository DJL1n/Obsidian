# SkelGS-SLAM — Decision Log

## 2026-06-07 — MASt3R-SLAM 论文分析结论

### 背景
完整阅读并整理了 MASt3R-SLAM (CVPR 2025) 论文，评估其作为 GS 前端的可行性。

### 关键判断

**MASt3R-SLAM 的定位：强 geometric proposal generator，不是 final certified GS geometry provider。**

#### 可采用的机制
1. **Ray residual** — GeometryPacket 的一致性应分为：
   - Strong term: angular / ray / bearing consistency
   - Weak term: depth / distance / scale consistency
   - Late term: GS rendering utility
   - 不要直接 3D point residual 主导 pose 或 anchor admission。

2. **Canonical pointmap fusion** — raw MASt3R packet → candidate packet → fused canonical packet → GS birth packet。GS 不应该直接吃 raw two-view output。

3. **Loop edge 需要 geometry verification** — retrieval candidate 不能直接成为约束。

4. **Scale/gauge 显式建模** — 不能只靠后处理 scalar。

#### 不建议照搬的
- MASt3R-SLAM 没有 full global geometry refinement
- 不输出 GS-ready uncertainty
- 不处理 dynamic ownership

### 对 DROID/DPVO 的定位
Temporal trust / lifecycle / uncertainty 更可能来自 DPVO/DROID-style signals，而非 MASt3R-SLAM。

### 后续方向
- 吸收 ray-consistency 和 canonical fusion 机制
- 保留 DPVO/DROID 的 temporal optimisation 优势
- 验证 anchor skeleton 中的 lifetime/uncertainty 信号来源

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MASt3R-SLAM]]

---

## 2026-06-07 — DROID-SLAM 论文分析结论

### 背景
完整阅读并整理了 DROID-SLAM (NeurIPS 2021) 论文，评估其 anchor/packet coherence 方向的 Temporal signal 价值。

### 关键判断

**DROID-SLAM 的定位：temporal optimisation backbone，不是 final geometry oracle。**

#### 最值得挖的不是深度图，而是优化痕迹
DROID-SLAM 每条 graph edge 提供的大量信号：
- w_ij: per-pixel / per-edge confidence
- r_ij: 网络认为当前几何投影需要修正多少
- BA residual: corrected correspondence 与投影位置的差距
- Δd: depth update magnitude
- Δξ: pose update magnitude
- λ: damping / local depth instability signal
- frame graph edge survival: covisibility stability
- flow distance: baseline / redundancy / keyframe spacing signal

这些可以映射到 anchor 机制：
- **admission**: low residual, high confidence, repeated visibility, small update magnitude, stable depth over window
- **rejection**: high residual, low confidence, large recurrent revision, inconsistent depth update, unstable covisibility

#### DROID vs MASt3R 的定位

| | MASt3R-SLAM | DROID-SLAM | DPVO |
|---|---|---|---|
| 信息来源 | two-view 3D prior | dense correlation + recurrent flow | sparse patch correlation |
| 匹配方式 | ray-based iterative projective | correlation volume + recurrent revision | patch graph + differentable BA |
| 几何优化 | second-order keyframe graph | differentiable dense BA | patch-level BA |
| 内参需求 | uncalibrated (generic camera) | 需要已知内参 | 需要已知内参 |
| 强项 | wide-baseline, uncalibrated | temporal optimisation, pose-depth joint | 轻量、高帧率 |
| 对 anchor 价值 | 两视图几何初始化 | temporal trust signal 最丰富 | 高帧率 temporal evidence |

#### 不建议照搬的
- DROID depth 不自动解决 GS birth coherence（depth 为 pose/depth consistency 服务，非 surface 优化）
- DROID confidence 是 learned BA weight，非完整 uncertainty，需二次校准
- DROID 计算成本高（~11 GB），长序列 dense graph 可能成瓶颈

### 研究架构定位更新
- **DROID/DPVO**: 时序优化骨架和 trust signal
- **MASt3R**: 强 two-view / loop / wide-baseline geometry proposal
- **Depth-normal predictor**: 更平滑 metric-like dense geometry
- **CertifiedGeometryPacket / Anchor skeleton**: 负责把这些信号变成 GS 可消费的稳定结构

### 后续方向（更新）
- DROID/DPVO 作为主时间骨架，获取 temporal trust signal
- 结合 MASt3R 的 wide-baseline geometry proposal
- Anchor admission/rejection 规则基于 residual/confidence/update/survival 组合
- 最终 GS geometry 必须经过 certification，不能直接相信 DROID depth

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/DROID-SLAM]]

---

## 2026-06-07 — S3LAM 论文分析结论

### 背景
完整阅读并整理了 S3LAM (arXiv 2021) 论文，评估其 semantic cluster + structure prior 对 anchor group 方向的启发。

### 关键判断

**S3LAM 的定位：不适合作为主 tracking/depth-pose frontend，适合作为 anchor group / structure prior / semantic support layer 的参考。**

#### 最值得吸收的机制
S3LAM 的核心思路：raw points/anchors → semantic-instance cluster → primitive hypothesis → support/inlier validation → soft structural factor → map/pose/GS birth confidence。

#### 对 anchor 方向的具体启发

**1. Cluster 比单点更可靠**
- single anchor: evidence weak，易受局部噪声/动态/深度错误影响
- anchor cluster: shared support，可拟合 primitive，可判断结构稳定性
- anchor 不一定只按像素/patch 独立成熟，可以有 anchor group / structural family

**2. Admission 应该是语义+几何双门控**
- segmentation pass + temporal visibility + depth consistency + normal consistency + primitive fit + inlier threshold
- 与 CertifiedGeometryPacket 思路契合

**3. 结构先验应该 soft（no-writeback）**
- plane prior 作为 candidate factor / confidence factor，只影响 admission/weighting/certification
- 不改 depth/pose/Gaussian

**4. Primitive abstraction 可扩展**
- S3LAM 只用了 plane，可扩展到：line/edge, quadric/ellipsoid, cuboid, local tangent patch
- 对 GS 最实用的是 local tangent plane / surfel primitive

#### DROID + S3LAM 互补

| DROID/DPVO | S3LAM-like structure |
|---|---|
| temporal evidence | semantic/structural grouping |
| BA residual/confidence | cluster-level support |
| depth update magnitude | primitive fit + inlier ratio |
| covisibility stability | maturity score |

### 研究架构定位更新
- **DROID/DPVO**: 时序优化骨架和 trust signal
- **MASt3R**: 强 two-view / loop geometry proposal
- **S3LAM-like**: semantic cluster + structural anchor group
- **Depth-normal predictor**: metric-like dense geometry prior
- **CertifiedGeometryPacket / Anchor skeleton**: admission + consistency gate
- **GS**: consume only stable structured anchors

### 后续方向（更新）
- DROID/DPVO 作为主时间骨架
- Structured Anchor Group 设计（group id, primitive, maturity score）
- 语义/结构双门控 admission pipeline
- 最终 GS geometry 必须经过 certification

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/S3LAM]]

---

## 2026-06-07 — ESLAM 论文分析结论

### 背景
完整阅读并整理了 ESLAM (CVPR 2023 Highlight) 论文，评估其对 geometry certification / free-space gating 方向的启发。

### 关键判断

**ESLAM 的定位：不适合作为 monocular tracking backbone，适合作为 geometry certification / surface-band gating 的思想来源。**

#### 可采用的机制
1. **Free-space gating** — candidate depth 之前的空间应为空，可用于 GS birth gating
2. **Surface-band check** — surface 附近 truncated band 应有 support，区分 high/low confidence
3. **Geometry/appearance 解耦** — 不要让 color/render loss 支配 geometry birth
4. **Occlusion check** — 不一致的深处不应 birth GS

#### 不建议照搬的
- ESLAM 是 RGB-D 系统，不解决 monocular scale
- Tracking 用 Adam 优化 render loss，无 DROID-style temporal BA trace
- 大场景 O(L²) 仍不是最终解

### 对五篇论文的完整定位

| 系统 | 对 SkelGS-SLAM 的价值 |
|---|---|
| **DROID/DPVO** | tracking / temporal anchor support |
| **MASt3R-SLAM** | two-view robust geometry proposal |
| **S3LAM** | semantic cluster + structural grouping |
| **ESLAM** | surface-band / free-space / implicit geometry regularization |

### 后续方向（更新）
- DROID/DPVO 作为主时间骨架
- MASt3R 作为宽基线几何来源
- S3LAM-like 语义结构分组
- ESLAM-like free-space + surface-band gating 加入 CertifiedGeometryPacket

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/ESLAM]]

---

## 2026-06-07 — LightGlue 论文分析结论

### 背景
完整阅读并整理了 LightGlue (ICCV 2023) 论文，评估其 sparse feature matching 在 SkelGS-SLAM 中的定位。

### 关键判断

**LightGlue 的定位：不是 SLAM backbone，而是强、快、适合工程集成的 sparse matching module。最适合放在 loop closure verification / relocalization / keyframe-pair validation / anchor sparse support evidence。**

#### 适合的用法
1. Keyframe-pair edge validation: LightGlue + RANSAC
2. Loop closure: retrieval → LightGlue verify → pose graph edge
3. Anchor maturity: 多视角下附近稳定 LightGlue inliers 计数
4. Submap overlap: sparse feature overlap 作为 cheap pre-check
5. Relocalization: current frame → map keyframes → LightGlue → PnP

#### 不建议的用法
- 不作为 monocular depth estimator
- 不作为 temporal tracker
- 不作为 BA optimizer
- 不作为 GS geometry certifier

### 六篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DROID/DPVO** | temporal optimization | tracking / temporal anchor support |
| **MASt3R-SLAM** | dense two-view geometry | robust geometry proposal |
| **S3LAM** | semantic cluster + structure | structural grouping |
| **ESLAM** | RGB-D TSDF implicit mapping | surface-band / free-space regularization |
| **LightGlue** | fast sparse matching | pair verification / loop / reloc |

### 后续方向（更新）
- DROID/DPVO 主时间骨架
- MASt3R 宽基线几何来源
- S3LAM-like 语义结构分组
- ESLAM-like free-space gating
- LightGlue 作为 side verification 模块

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/LightGlue]]

---

## 2026-06-07 — Scaffold-GS 论文分析结论

### 背景
完整阅读并整理了 Scaffold-GS (CVPR 2024 Highlight) 论文，评估其 anchor-structured Gaussian 表示对 SkelGS-SLAM 的 GS backend / ChildGS 方向的启发。

### 关键判断

**Scaffold-GS 的定位：不是 SLAM frontend 的答案，是"结构化 GS backend"的重要参考。对 SkelGS-SLAM 的价值在于 ChildGS / anchor-conditioned Gaussian birth，而非 pose tracking 或 geometry packet certification。**

#### 最值得采用的机制
1. **Anchor-conditioned Gaussian birth** — certified anchor → local offsets → spawn child Gaussians，而非 depth pixel → directly birth Gaussian
2. **ChildGS 形式** — parent anchor (position, normal, feature, scale, support) → child Gaussians (offset in local tangent frame, anisotropic scale, opacity, SH)
3. **View-adaptive appearance allowed, view-adaptive geometry dangerous** — geometry slow-changing multi-view consistent
4. **Growing/pruning gate 需 evidence-gated** — temporal + geometric + render gradient

#### 与你的 anchor skeleton 的根本区别

| | Scaffold-GS anchor | 你的 geometric anchor |
|---|---|---|
| 目标 | rendering organization | geometry certification |
| 生长依据 | rendering gradient | track survival + residual + consistency |
| 剪枝依据 | opacity contribution | geometry support + observation count |

#### 不建议照搬的
- SfM voxel 初始化（你的 anchor 应来自 tracking signal）
- 纯 rendering gradient growing
- View-adaptive geometry（可能掩盖真实几何错误）

### 七篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DROID/DPVO** | temporal optimization | tracking / temporal anchor support |
| **MASt3R-SLAM** | dense two-view geometry | robust geometry proposal |
| **S3LAM** | semantic cluster + structure | structural grouping |
| **ESLAM** | RGB-D TSDF implicit mapping | surface-band / free-space regularization |
| **LightGlue** | fast sparse matching | pair verification / loop / reloc |
| **Scaffold-GS** | structured GS backend | anchor-conditioned GS birth / ChildGS |

### 后续方向（更新）
- DROID/DPVO 主时间骨架
- MASt3R 宽基线几何来源
- S3LAM-like 语义结构分组
- ESLAM-like free-space gating
- LightGlue side verification
- **Scaffold-GS-like anchor-conditioned ChildGS**

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/Scaffold-GS]]

---

## 2026-06-07 — DPVO 论文分析结论

### 背景
完整阅读并整理了 DPVO (NeurIPS 2023) 论文，评估其作为 SkelGS-SLAM anchor temporal backbone 的价值。

### 关键判断

**DPVO 的定位：最合适的 anchor temporal trust backbone。比 DROID 更轻（60-120 FPS, 29-57% memory）、比 MASt3R 更适合高频 tracking、比 LightGlue 提供完整 lifecycle。**

#### 最值得采用的机制
1. **Patch lifecycle** — source frame, connected frames, revision δ, confidence Σ, depth update, BA residual, survival time
2. **Patch graph** — 局部 temporal edges + graph-based aggregation
3. **Differentiable BA** — pose + patch inverse depth 联合优化，提供 residual/depth update signal
4. **Shadow diagnostics 路径** — 先只记录不写回，验证 AnchorScore 再做 admission

#### 合理流程
```
DPVO patch → anchor candidate
DPVO evidence + depth/normal + multi-view + free-space gate → certified anchor
certified anchor → child Gaussian birth
```

#### 不建议的用法
- DPVO patch depth → 直接 birth Gaussian（无 geometry certification）

### 八篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DPVO** | sparse patch recurrent VO | **temporal tracking + anchor trust backbone** |
| DROID-SLAM | dense recurrent pose-depth | richer but heavier temporal signal |
| MASt3R-SLAM | dense two-view geometry | robust geometry proposal |
| S3LAM | semantic cluster + structure | structural grouping |
| ESLAM | RGB-D TSDF implicit mapping | surface-band / free-space regularization |
| LightGlue | fast sparse matching | pair verification / loop / reloc |
| Scaffold-GS | structured GS backend | anchor-conditioned GS birth / ChildGS |

### 后续方向（更新）
- **DPVO 作为主时间骨架** — patch lifecycle → anchor maturity
- MASt3R 宽基线几何来源
- S3LAM-like 语义结构分组
- ESLAM-like free-space gating
- LightGlue side verification
- Scaffold-GS-like anchor-conditioned ChildGS

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/DPVO]]

---

## 2026-06-07 — Gaussian-SLAM 论文分析结论

### 背景
完整阅读并整理了 Gaussian-SLAM (ECCV 2024) 论文，评估其 GS mapping / online seeding / submap optimization 对 SkelGS-SLAM 的启发。

### 关键判断

**Gaussian-SLAM 的定位：RGB-D 条件下把 3DGS 做进 dense SLAM 的重要系统。最有用的是 Gaussian birth gate、submap optimization、alpha-masked tracking。不能替代 DPVO/DROID，不能直接解决 monocular geometry packet 认证。**

#### 最值得借鉴
1. **Controlled Gaussian seeding** — alpha mask + NN check，不照搬 gradient split/clone
2. **Alpha mask for tracking feedback** — 避免坏 map 污染 pose
3. **Sub-map active optimization** — 只优化当前活跃区域

#### 不建议照搬
- RGB-D point cloud → seed Gaussian（你的系统无真实 depth）
- Render-and-optimize tracking（DPVO/DROID 更适合 monocular）

### 九篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DPVO** | sparse patch recurrent VO | **temporal tracking + anchor trust backbone** |
| DROID-SLAM | dense recurrent pose-depth | richer but heavier temporal signal |
| MASt3R-SLAM | dense two-view geometry | robust geometry proposal |
| S3LAM | semantic cluster + structure | structural grouping |
| ESLAM | RGB-D TSDF implicit | surface-band / free-space regularization |
| LightGlue | fast sparse matching | pair verification / loop / reloc |
| Scaffold-GS | structured GS backend | anchor-conditioned GS birth / ChildGS |
| **Gaussian-SLAM** | **RGB-D online GS SLAM** | **birth gate / submap / alpha-mask tracking** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/Gaussian-SLAM]]

---

## 2026-06-07 — GO-SLAM 论文分析结论

### 背景
完整阅读并整理了 GO-SLAM (ICCV 2023) 论文，评估其 online loop closure / full BA / global geometry correction 对 SkelGS-SLAM 的价值。

### 关键判断

**GO-SLAM 的定位：global geometry correction architecture reference。最值得借鉴的是 online loop closure + full BA + versioned mapping refresh，这是 DPVO 短窗口和 GS backend 之间缺失的"全局几何一致性中层"。**

#### 最值得借鉴
1. **Online global keyframe graph correction** — DPVO 短窗口 drift 需 loop + global BA 补
2. **Mapping 对 pose/depth 更新敏感** — CoVersionedGeometryPacket 的系统级理由
3. **Keyframe selection for refresh** — latest + unoptimized + pose-diff top 10 + stratified
4. **SDF/free-space loss → GS birth gate**

#### 不建议照搬
- DROID dense frontend（太贵，DPVO 更轻）
- Implicit SDF map（GS 需要 explicit Gaussian）
- Mapping 直接信任 BA 输出（仍需 certification）

### 十篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DPVO** | sparse patch recurrent VO | temporal tracking + anchor trust backbone |
| DROID-SLAM | dense recurrent pose-depth | richer but heavier temporal signal |
| MASt3R-SLAM | dense two-view geometry | robust geometry proposal |
| S3LAM | semantic cluster + structure | structural grouping |
| ESLAM | RGB-D TSDF implicit | surface-band / free-space regularization |
| LightGlue | fast sparse matching | pair verification / loop / reloc |
| Scaffold-GS | structured GS backend | anchor-conditioned GS birth / ChildGS |
| Gaussian-SLAM | RGB-D online GS SLAM | birth gate / submap / alpha-mask |
| **GO-SLAM** | **online global learned LC/BA** | **global geometry correction + versioned mapping** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/GO-SLAM]]

---

## 2026-06-07 — SplaTAM 论文分析结论

### 背景
完整阅读并整理了 SplaTAM (CVPR 2024) 论文，评估其 silhouette-gated tracking / GS densification / isotropic GS-SLAM 对 SkelGS-SLAM 的启发。

### 关键判断

**SplaTAM 的定位：GS backend / silhouette-gated tracking / densification reference。不是 monocular frontend / temporal optimizer / geometry certifier。**

#### 最值得借鉴
1. **Silhouette mask 作为 GS feedback 安全门** — only map-covered regions participate in tracking
2. **Densification mask → monocular Gaussian birth gate** — alpha low + certified geometry → birth
3. **RGB-only render quality ≠ geometry good** — PSNR 高 ≠ depth/geometry 可靠
4. **Map update 选 overlap keyframes**

#### 不建议照搬
- RGB-D depth → Gaussian seeding（无真实 depth）
- Isotropic Gaussian（monocular GS 可能需要 anisotropy）
- No loop closure / global BA

### 十一篇论文完整定位

| 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|---|---|
| **DPVO** | sparse patch recurrent VO | temporal tracking + anchor trust backbone |
| DROID-SLAM | dense recurrent pose-depth | richer but heavier temporal signal |
| MASt3R-SLAM | dense two-view geometry | robust geometry proposal |
| S3LAM | semantic cluster + structure | structural grouping |
| ESLAM | RGB-D TSDF implicit | surface-band / free-space regularization |
| LightGlue | fast sparse matching | pair verification / loop / reloc |
| Scaffold-GS | structured GS backend | anchor-conditioned GS birth / ChildGS |
| Gaussian-SLAM | RGB-D online GS SLAM | birth gate / submap / alpha-mask |
| GO-SLAM | online global learned LC/BA | global geometry correction + versioned mapping |
| **SplaTAM** | **RGB-D GS SLAM** | **silhouette gate / densification / isotropic GS** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/SplaTAM]]

---

## 2026-06-07 — MASt3R (base model) 分析结论

### 背景
完整阅读了 MASt3R (ECCV 2024) 论文 — 这是 MASt3R-SLAM 底层的 two-view model。评估其 wide-baseline pairwise geometry 对 SkelGS-SLAM 的定位。

### 关键判断

**MASt3R 的定位：strong pairwise 3D geometry proposal source。应作为 wide-baseline geometric witness，与 DPVO temporal witness、depth-normal surface witness 一起进入 CertifiedGeometryPacket，而非单独成为 GS 前端的最终底座。**

#### 最适合作为
- Keyframe pair geometry proposal
- Loop candidate verification
- Submap overlap verification
- Local dense depth/pointmap candidate

#### 不适合作为
- High-rate frame-to-frame tracker
- Direct GS birth source
- Long-window trajectory source

#### 定位对比
| MASt3R | vs LightGlue | vs DROID/DPVO |
|---|---|---|
| dense 3D-grounded | sparse 2D | temporal optimizer |
| wide-baseline 强 | 快、轻 | temporal signal |
| geometry proposal | loop/reloc | tracking backbone |

### 12 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **main temporal backbone** |
| 2 | **MASt3R** | pairwise 3D geometry proposal | **wide-baseline geometric witness** |
| 3 | MASt3R-SLAM | monocular dense SLAM | system reference |
| 4 | DROID-SLAM | dense recurrent pose-depth | richer temporal signal |
| 5 | S3LAM | semantic cluster + structure | structural grouping |
| 6 | ESLAM | RGB-D TSDF implicit | surface-band / free-space |
| 7 | LightGlue | fast sparse matching | pair verification / loop |
| 8 | Scaffold-GS | structured GS backend | anchor-conditioned ChildGS |
| 9 | Gaussian-SLAM | RGB-D online GS SLAM | birth gate / submap |
| 10 | GO-SLAM | online global LC/BA | global correction + versioning |
| 11 | SplaTAM | RGB-D GS SLAM | silhouette gate / densification |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MASt3R]]

---

## 2026-06-07 — MonoGS 分析结论

### 背景
完整阅读了 Gaussian Splatting SLAM / MonoGS (CVPR 2024)。评估其 monocular-first 3DGS SLAM 对 SkelGS-SLAM 的参考价值。

### 关键判断

**MonoGS 的定位：3DGS map-centric SLAM reference / GS tracking convergence / covisibility / regularisation reference。不是 temporal frontend / geometry certifier。**

#### 最值得借鉴
1. **GS map 作为 tracking 表示的收敛盆大** — GS feedback 不是完全不可用
2. **Gaussian covisibility → submap/keyframe overlap**
3. **Isotropic regularisation 是 GS 几何安全底线**
4. **暴露了"先 birth 后 prune"的风险 — 你的改进空间正在这里**

#### 不建议照搬
- Monocular rendered depth insertion（太危险）
- 无 loop closure 的纯 local mapping
- 无 temporal 前端的 render-and-optimise tracking

### 13 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **main temporal backbone** |
| 2 | **MASt3R** | pairwise 3D geometry proposal | wide-baseline witness |
| 3 | MASt3R-SLAM | monocular dense SLAM | system reference |
| 4 | DROID-SLAM | dense recurrent pose-depth | richer temporal signal |
| 5 | S3LAM | semantic cluster + structure | structural grouping |
| 6 | ESLAM | RGB-D TSDF implicit | surface-band / free-space |
| 7 | LightGlue | fast sparse matching | pair verification / loop |
| 8 | Scaffold-GS | structured GS backend | anchor-conditioned ChildGS |
| 9 | Gaussian-SLAM | RGB-D online GS SLAM | birth gate / submap |
| 10 | GO-SLAM | online global LC/BA | global correction + versioning |
| 11 | SplaTAM | RGB-D GS SLAM | silhouette gate / densification |
| 12 | **MonoGS** | **monocular-first 3DGS SLAM** | **GS tracking / covisibility / regularisation** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MonoGS]]

---

## 2026-06-07 — Spann3R 分析结论

### 背景
完整阅读了 Spann3R (3DV 2025) 论文。评估其 spatial memory design 对 anchor skeleton / packet memory 的启发。

### 关键判断

**Spann3R 的定位：learned spatial memory / global pointmap proposal reference。最值得借鉴的不是拿它做前端，而是它的内存设计：working + long-term + attention + clipping + sparsification。但缺少 BA / loop correction / GS birth certification。**

#### 最值得借鉴
1. **Spatial memory 结构** — working (dense recent) + long-term (sparse top-k) + attention query
2. **Attention clipping** — outlier hard-reject 对应你的 certified packet gate
3. **Memory sparsification** — 只保留长期有用 tokens
4. **Drain 机制** — working → long-term 对应 anchor maturity promotion

#### 不建议照搬
- 无 BA / global correction
- Direct global pointmap regression → GS（缺少 certification）

### 14 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **main temporal backbone** |
| 2 | **MASt3R** | pairwise 3D geometry | wide-baseline witness |
| 3 | **Spann3R** | **learned spatial memory reconstruction** | **memory abstraction / global pointmap proposal** |
| 4+ | MASt3R-SLAM to SplaTAM | various | references |
| **14** | **Spann3R** | **memory / proposal** | **anchor skeleton memory design** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/Spann3R]]
