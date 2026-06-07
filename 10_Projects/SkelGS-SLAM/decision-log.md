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

---

## 2026-06-07 — OG-Mapping 分析结论

### 背景
完整阅读了 OG-Mapping (arXiv 2024) 论文。评估其 octree-structured anchor hierarchy / progressive LOD / dynamic window 对 SkelGS-SLAM 的价值。

### 关键判断

**OG-Mapping 的定位：GS backend / structured anchor growth reference。最值得借鉴的是 octree-organized anchor hierarchy + progressive LOD + dynamic packet window。不是 tracking frontend / monocular solution。**

#### 最值得借鉴
1. **Octree anchor hierarchy** — flat anchor list → 多尺度 spatial index
2. **Progressive LOD growth** — level-aware, coarse → fine
3. **Dynamic keyframe window → dynamic packet window**
4. **支持"先认证，再 birth" — 降低对 noisy depth 的直接依赖**

#### 不建议照搬
- RGB-D depth 直接建 octree
- GT/external pose assumption
- 只做 mapping 不处理 tracking drift

### 15 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **main temporal backbone** |
| 2 | MASt3R | pairwise 3D geometry | wide-baseline witness |
| 3 | Spann3R | spatial memory recon. | memory abstraction |
| 4–12 | others | various | references |
| **15** | **OG-Mapping** | **octree-structured GS mapping** | **anchor octree / LOD growth / dynamic window** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/OG-Mapping]]

---

## 2026-06-07 — DUSt3R 分析结论

### 背景
完整阅读了 DUSt3R (CVPR 2024) 论文 — MASt3R/Spann3R 的底层 foundation model。评估其 pairwise pointmap regression 对 SkelGS-SLAM 的定位。

### 关键判断

**DUSt3R 的定位：foundational pairwise dense 3D prior。对 GS-SLAM 来说只能是强 geometry proposal，不能是最终 geometry truth；必须经过 temporal/scale/normal/free-space/global consistency 认证才能进入 CertifiedGeometryPacket。**

#### 最适合作为
- Dense two-view geometry candidate
- Depth candidate / point cloud candidate
- Wide-baseline edge proposal
- DPVO drift 的外部对照
- Anchor candidate 的 pairwise support

#### 不适合
- 高频 tracking / 长窗口 trajectory / 直接 GS input

#### 三层模型关系
| 模型 | DUSt3R | MASt3R | Spann3R |
|---|---|---|---|
| 核心 | pointmap prior | + matching head | + spatial memory |
| 匹配精度 | 低 | 高 | 中 |
| 全局坐标 | pairwise | pairwise | global (memory) |
| 对你价值 | geometry proposal | matching witness | memory abstraction |

### 16 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **main temporal backbone** |
| **2** | **DUSt3R** | **pairwise dense 3D foundation prior** | **foundational geometry proposal** |
| 3 | MASt3R | DUSt3R + matching | wide-baseline witness |
| 4–16 | others | various | references |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/DUSt3R]]

---

## 2026-06-07 — ContextGS 分析结论

### 背景
完整阅读了 ContextGS (NeurIPS 2024) 论文。评估其 anchor-level autoregressive context compression 对 SkelGS-SLAM frozen submap / anchor memory 的启发。

### 关键判断

**ContextGS 的定位：frozen/mature anchor-GS map compression reference。最值得借鉴的是 hierarchical anchor partition + autoregressive context model + rate-distortion trade-off。不是 SLAM frontend / geometry certification / online mapping。**

#### 最值得借鉴
1. **Anchor memory 需要压缩和层级化** — active → mature → frozen/context-coded
2. **Anchor 之间可以互相预测** — context model 用于 compression + consistency prior
3. **Frozen submap → ContextGS-style compressed anchors**
4. **Rate-distortion trade-off for GS maps**

#### 不建议照搬
- 作为 SLAM frontend / tracking optimizer / geometry certifier

### 17 篇论文完整定位

| # | 系统 | 定位 |
|---|------|------|
| 1 | **DPVO** | temporal backbone |
| 2–3 | **DUSt3R / MASt3R** | geometry proposal |
| 4–6 | Spann3R / MASt3R-SLAM / DROID | memory / system / temporal |
| 7–10 | S3LAM / ESLAM / LightGlue / GO-SLAM | structure / surface / verify / correct |
| 11–16 | Scaffold-GS / GS-SLAMs / OG-Mapping / MonoGS / SplaTAM | GS backend variants |
| **17** | **ContextGS** | **anchor-level context compression / frozen map** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/ContextGS]]

---

## 2026-06-07 — GS-SLAM (RGB-D) 分析结论

### 背景
完整阅读了 GS-SLAM (CVPR 2024)。评估其 adaptive expansion / coarse-to-fine tracking / RGB-D pipeline 对 SkelGS-SLAM 的参考价值。

### 关键判断

**GS-SLAM 的定位：RGB-D 3DGS SLAM early baseline。证明 3DGS 可进入 RGB-D SLAM 主循环。最值得借鉴的是 adaptive expansion、floating Gaussian deletion、coarse-to-fine reliable Gaussian tracking。但不能直接作为 monocular 答案 — 核心安全来源是 sensor depth。**

#### 最值得借鉴
1. **Adaptive expansion: adding + deletion** → CertifiedPacket-guided expansion + anchor-level quarantine
2. **Coarse-to-fine tracking** → GS feedback 必须只用 reliable Gaussians + gated
3. **BA-like random keyframe** → dynamic packet window (结合 GO-SLAM/OG-Mapping)

#### 不建议照搬
- RGB-D depth-driven birth/deletion
- Direct map-based tracking as main frontend
- SH-heavy explicit Gaussian without compression

### 18 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | **DPVO** | temporal tracking | **主 temporal backbone** |
| 2–3 | **DUSt3R/MASt3R** | geometry proposal | wide-baseline witness |
| 4–7 | Spann3R/MASt3R-SLAM/DROID/S3LAM | memory/system/temporal/structure |
| 8–10 | ESLAM/LightGlue/GO-SLAM | surface/verify/correct |
| 11–17 | Scaffold-GS → ContextGS | GS backend variants |
| **18** | **GS-SLAM** | **RGB-D 3DGS SLAM early baseline** | **adaptive expansion / reliable GS tracking** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/GS-SLAM]]

---

## 2026-06-07 — MGS-SLAM 分析结论

### 背景
完整阅读了 MGS-SLAM (IEEE RA-L 2024)。评估其 DPVO + MVS depth + GS mapping + SDAR 对 SkelGS-SLAM 的参考价值。

### 关键判断

**MGS-SLAM 是目前最接近"DPVO + monocular Gaussian mapping"的参考系统。最值借鉴：DPVO GS 分工 + SDAR scale closure + depth smooth loss。但还不够安全 — SDAR 是统计校正，你需要的是 CertifiedGeometryPacket / CertifiedAnchor。**

#### 最值借鉴
1. **DPVO frontend + GS backend 分工** — 支持你的路线
2. **SDAR: sparse-dense scale closure** — 你 scale coherence 的参考
3. **Depth smooth loss** — predicted depth 不能硬吃
4. **Tracking vs mapping keyframe 分离**

#### 需加强
- SDAR → multi-factor certified alignment
- GS feedback → gated weak evidence only

### 19 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **主 backbone** |
| 2 | DUSt3R→MASt3R | geometry proposal | wide-baseline witness |
| **19** | **MGS-SLAM** | **monocular DPVO+MVS+GS** | **最接近你路线的参考系统** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MGS-SLAM]]

---

## 2026-06-07 — VPGS-SLAM 分析结论

### 背景
完整阅读了 VPGS-SLAM (arXiv 2025)。评估其 large-scale submap / voxel-anchor / 2D-3D tracking / loop fusion 对 SkelGS-SLAM 的价值。

### 关键判断

**VPGS-SLAM 的定位：large-scale 3DGS-SLAM framework。最值借鉴的是 submap-as-geometry-unit、voxel-anchor ChildGS、evidence-adaptive tracking、loop + submap fusion。但非 monocular — 3D input来自 RGB-D/point cloud，你先要 CertifiedGeometryPacket。**  

#### 最值借鉴
1. **Submap 是 local geometry unit** — 不只是一个窗口
2. **ChildGS 应由 voxel/anchor/LOD 控制**
3. **Tracking evidence quality adaptive** — 不同信号动态权重
4. **Loop 后修 pose + Gaussian submap** — CoVersionedGeometryPacket 的系统意义
5. **Submap fusion 需 distillation** — 不只是 pose 对齐

#### 不建议照搬
- Predicted depth → VPGS 3D input
- Voxel ICP without scale/static verification
- Loop fuse without geometry packet versioning

### 20 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | backbone |
| 2–3 | DUSt3R→MASt3R | geometry proposal | witness |
| 4–20 | various | various | references |
| **20** | **VPGS-SLAM** | **large-scale 3DGS-SLAM** | **submap/anchor/2D-3D/loop fusion** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/VPGS-SLAM]]

---

## 2026-06-07 — GS-SDF 分析结论

### 背景
完整阅读了 GS-SDF (arXiv 2025)。评估其 LiDAR→NSDF→GS init+shape reg 对 SkelGS-SLAM 的启发。

### 关键判断

**GS-SDF 的定位：LiDAR-visual reconstruction/rendering。最值借鉴：外部连续几何场监督 GS + shape regularization > center regularization + 先锁 geometry 再学 appearance + 初始化设计精细。但依赖 LiDAR/posed images，非 monocular SLAM frontend。**

#### 最值借鉴
1. **GS 几何需要外部连续几何场监督** — render-derived 不够
2. **Shape regularization > center regularization** — ChildGS 应约束整个 tangent plane
3. **初始化比后期正则更重要** — birth 时就要有 normal/tangent/scale
4. **先锁 geometry 再训练 appearance** — 防止 early photo loss 拉坏几何
5. **Sky/background 单独建模**

#### LiDAR → CertifiedGeometryField
你的 monocular 版应构造 CertifiedGeometryField / CertifiedAnchorField 作为 weak geometry teacher，ChildGS 只在这个场约束内生长。

### 21 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–20 | various | various | references |
| **21** | **GS-SDF** | **LiDAR→NSDF→GS init+shape reg** | **geometry teacher / shape reg** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/GS-SDF]]

---

## 2026-06-07 — MG-SLAM 分析结论

### 背景
完整阅读了 MG-SLAM (arXiv 2025)。评估其 Manhattan World + line/plane structure prior 对 SkelGS-SLAM 的启发。

### 关键判断

**MG-SLAM 的定位：RGB-D GS + Manhattan structure。最值借鉴：结构先验从"辅助语义"升级为"Gaussian birth 条件"、线段定义 anchor group boundary、补全必须标记为 hypothesized。但依赖 RGB-D / Manhattan 假设 / 非 monocular。**

#### 最值借鉴
1. **结构先验 → Gaussian birth 条件** — CertifiedAnchorGroup + structure → ChildGS
2. **线段作为 anchor group boundary** — 补充 DPVO/MASt3R 不擅长的结构边界
3. **Observed vs Hypothesized geometry 分层** — 前者可强约束 pose，后者仅 rendering
4. **Manhattan 应是 weak prior** — evidence-gated
5. **支持 anchor group > isolated anchor**

#### 不建议照搬
- RGB-D backprojection
- Manhattan completion as truth
- PointNet++ color interp
- Hypothesized Gaussians → strong tracking

### 22 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–21 | various | various | references |
| **22** | **MG-SLAM** | **RGB-D GS + Manhattan structure** | **line/plane completion / structure prior** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MG-SLAM]]

---

## 2026-06-07 — SLAM3R 分析结论

### 背景
完整阅读了 SLAM3R (CVPR 2025 Highlight)。评估其 I2P + L2W + retrieval-based dense reconstruction 对 SkelGS-SLAM 的参考价值。

### 关键判断

**SLAM3R 的定位：RGB-only real-time dense reconstruction。最值借鉴：multi-frame I2P local geometry packet、learned L2W global registration、retrieval-based reference selection。但不是 GS-SLAM / DPVO temporal backbone / explicit BA system。proposal ≠ certificate。**

#### 最值借鉴
1. **I2P multi-frame local window → candidate packet**
2. **L2W local→global registration → CoVersionedGeometryPacket**
3. **Retrieval-based reference selection → historical anchor/packet retrieval**
4. **支持"proposal ≠ certificate"判断**

#### 不建议照搬
- No explicit BA / graph correction as main system
- Replace DPVO temporal backbone
- SLAM3R pointmap → direct GS birth

### 23 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–22 | various | various | references |
| **23** | **SLAM3R** | **RGB-only dense recon.** | **dense proposal / learned reg / retrieval** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/SLAM3R]]

---

## 2026-06-07 — GPS-SLAM 分析结论

### 背景
完整阅读了 GPS-SLAM / Gaussian-Plus-SDF SLAM (CVPR 2025)。评估其 SDF + residual GS 设计对 SkelGS-SLAM 的启发。

### 关键判断

**GPS-SLAM 的定位：RGB-D ultra-fast reconstruction via SDF + residual GS。最值借鉴：GS 不一定是主几何 — 先 geometry 后 GS residual。完全支持你 "CertifiedGeometryPacket before GS" 的方向。**

#### 最值借鉴
1. **GS 不一定是主几何** — SDF geometry + GS residual appearance
2. **Geometry-first pipeline** — 你的 CertifiedGeometryPacket 等价于 SDF
3. **ChildGS 可重新定义为 residual GS** — 只在 certified surface 上修正 color
4. **Depth-culling → free-space / surface-band gate**
5. **Gaussian birth = residual error trigger on certified geometry**

#### 不建议照搬
- RGB-D SDF fusion / ICP tracking
- Sensor depth as geometry truth
- No monocular scale/pose-depth coherence handling

### 24 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–23 | various | various | references |
| **24** | **GPS-SLAM** | **SDF + residual GS** | **geometry-first / GS-as-residual** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/GPS-SLAM]]

---

## 2026-06-07 — CUT3R 分析结论

### 背景
完整阅读了 CUT3R (CVPR 2025 Oral)。评估其 persistent state + continuous pointmap 对 SkelGS-SLAM 的价值。

### 关键判断

**CUT3R 的定位：persistent recurrent stateful pointmap predictor。最值借鉴：persistent state → CertifiedGeometryMemory、unseen inference → hypothesis only、continous dense proposal。但不能替代 DPVO temporal backbone / provide anchor lifecycle / serve as GS birth truth。**

#### 最值借鉴
1. **Persistent state → 你的 CertifiedGeometryMemory**
2. **Unseen inference → Hypothesis, not Certified**
3. **Continuous dense geometry witness → CandidatePacket**
4. **Failure detector: DPVO stable + CUT3R inconsistent → risk**

#### 不建议照搬
- CUT3R → DPVO replacement（你之前 PSNR 13.93 说明直接当 GS input 危险）
- CUT3R → direct GS birth
- Latent state → un-auditable anchor memory

### 25 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–24 | various | various | references |
| **25** | **CUT3R** | **persistent state pointmap** | **continuous stateful proposal / memory reference** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/CUT3R]]

---

## 2026-06-07 — SEGS-SLAM 分析结论

### 背景
完整阅读了 SEGS-SLAM (ICCV 2025)。评估其 structured GS + AfME + frequency pyramid 对 SkelGS-SLAM 的启发。

### 关键判断

**SEGS-SLAM 的定位：structured GS + appearance embedding backend。最值借鉴：structured GS for SLAM、AfME appearance-geometry decoupling、frequency pyramid controlled growth。但不是几何认证框架 / high-rate temporal frontend / DPVO alternative。**

#### 最值借鉴
1. **Structured GS → ChildGS 应是 anchor-conditioned**
2. **AfME → appearance variation 单独建模，减少 RGB loss 对几何污染**
3. **Frequency pyramid → CertifiedAnchor LOD pyramid**
4. **Appearance embedding 边界：可解释 color/view 差异，不可认证几何**

#### 不建议照搬
- Structured GS 替代 geometry certification
- AfME 提升 PSNR → 认为 geometry 更好
- SEGS-SLAM 替代 DPVO/DROID

### 26 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–25 | various | various | references |
| **26** | **SEGS-SLAM** | **structured GS + AfME** | **ChildGS / appearance decoupling / frequency growth** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/SEGS-SLAM]]

---

## 2026-06-07 — HI-SLAM2 分析结论

### 背景
完整阅读了 HI-SLAM2 (IEEE T-RO 2025)。评估其 dense SLAM + JDSA + PGBA + 3DGS + deformation 对 SkelGS-SLAM 的参考价值。

### 关键判断

**HI-SLAM2 是目前最接近"monocular RGB + dense tracking + predicted geometry + 3DGS + global correction"的完整系统。最值借鉴：JDSA spatially varying scale alignment、Gaussian deformation after pose update、normal prior GS supervision、层级优化（local BA→PGBA→full BA→joint refine）。不是 certification framework — aligned estimated depth 仍不等于 certified geometry。你的升级空间：multi-evidence certified geometry → Gaussian。**

#### 最值借鉴
1. **JDSA grid-scale alignment** → 你的 multi-evidence Certified Scale Alignment
2. **Gaussian map deformation** → CoVersionedGeometryPacket
3. **Normal prior → GS geometry**
4. **层级优化结构完整**

#### 升级空间
- HI-SLAM2: aligned estimated depth → Gaussian
- 你: multi-source certified geometry → Gaussian

### 27 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–26 | various | various | references |
| **27** | **HI-SLAM2** | **mono dense + priors + 3DGS** | **最重要系统参考 / JDSA / deformation** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/HI-SLAM2]]

---

## 2026-06-07 — WildGS-SLAM 分析结论

### 背景
完整阅读了 WildGS-SLAM (CVPR 2025)。评估其 DINOv2 uncertainty + DROID + GS + dynamic filtering 对 SkelGS-SLAM 的价值。

### 关键判断

**WildGS-SLAM 的定位：dynamic-scene monocular GS-SLAM via uncertainty awareness。最值借鉴：DINOv2-based per-sequence uncertainty（无语义类别）、uncertainty 同时用于 DBA + GS mapping、predicted depth as stabilizer not truth。不是 geometry certification — uncertainty 是 reliability weight，不是 strict dynamic/static label。**

#### 最值借鉴
1. **Uncertainty → CandidatePacket dynamic risk gate**
2. **Dynamic 区域不入 CertifiedAnchor**
3. **Predicted depth = early stabilizer, final multiview > monocular prior**
4. **GS feedback 和 uncertainty 独立优化**

#### 升级空间
- WildGS uncertainty alone not enough → 融合 temporal + multi-view + free-space + normal + anchor survival + loop

### 28 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–27 | various | various | references |
| **28** | **WildGS-SLAM** | **mono dynamic GS-SLAM** | **uncertainty / dynamic removal** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/WildGS-SLAM]]

---

## 2026-06-07 — UP-SLAM 分析结论

### 背景
完整阅读了 UP-SLAM (ICRA 2026)。评估其 probabilistic octree anchor + training-free uncertainty + DINO feature map + parallel tracking-mapping 对 SkelGS-SLAM 的启发。

### 关键判断

**UP-SLAM 的定位：RGB-D dynamic GS-SLAM。最值借鉴：probabilistic anchor update（Bayesian）、multi-modal uncertainty、DINO feature distillation、tracking/mapping 解耦。但不是 monocular / DPVO/DROID temporal backbone / geometry certification framework。**

#### 最值借鉴
1. **ProbabilisticAnchor → 你的 CertifiedAnchor 概率更新形式**
2. **概率更新替代硬阈值 — 你的 monocular 系统更应避免手工 depth conf 阈值**
3. **DINO feature distillation → anchor 可扩展语义通道**
4. **tracking/mapping 解耦 → 支持 no-writeback**
5. **Training-free estimator → early uncertainty gate**

#### 不够
- RGB-D / ORB-SLAM3 / YOLOv8-seg 部分依赖 — 不能直接搬到 mono
- ProbabilisticAnchor motion prob ≠ CertifiedAnchor geometry cert

### 29 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–28 | various | various | references |
| **29** | **UP-SLAM** | **RGB-D dynamic GS-SLAM** | **probabilistic anchor / multi-modal uncertainty** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/UP-SLAM]]

---

## 2026-06-07 — GSO-SLAM 分析结论

### 背景
完整阅读了 GSO-SLAM (IEEE RA-L 2026)。评估其 DSO + 2DGS EM bidirectional coupling + GS init from VO 对 SkelGS-SLAM 的启发。

### 关键判断

**GSO-SLAM 的定位：DSO + 2DGS under EM coupling。最值借鉴：EM bidirectional VO-GS coupling、Gaussian shape init from VO gradients/associations、2DGS surface-aware map。不是 DPVO temporal backbone / geometry certification framework / global correction system。**

#### 最值借鉴
1. **EM coupling: VO depth ↔ GS depth 双向** — 安全版本需加强 gate
2. **Gaussian 初始化不只是 center** — 从 frontend gradients/associations 估计 shape
3. **2DGS 更适合 surface-aware mapping**
4. **无额外深度预测网络** — 纯 DSO+2DGS

#### 不适合照搬
- DSO as sole frontend (motion blur/noise/exposure)
- Semi-dense only coverage (textureless 不足)
- GS depth feedback without certification

### 30 篇论文完整定位

| # | 系统 | 定位 | 对 SkelGS-SLAM 价值 |
|---|------|------|---------------------|
| 1 | DPVO | temporal tracking | **backbone** |
| 2–29 | various | various | references |
| **30** | **GSO-SLAM** | **DSO+2DGS EM coupling** | **bidirectional coupling / GS init** |

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/GSO-SLAM]]
