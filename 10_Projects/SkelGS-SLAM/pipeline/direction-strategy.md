# Pipeline 方向决策：从 MASt3R 基座转向 AnchorGraph 中心

记录时间：2026-06-05（Codex Execution Decision）+ 后续 GPT Pro 战略讨论
主题：放弃 MASt3R 作为主前端，转向 Anchor-DSO + AnchorGraph 中心的系统架构

---

## 0. 核心结论

**不再围绕 MASt3R/DROID/GSO 设计主系统。**
主前端自己造：**Anchor-DSO**（DSO-style sparse direct + mature anchor support）。
第二前端：DPVO-style sparse patch BA（不是 full DROID）。
低频强校正：MASt3R 降级为 oracle。
GS：只作为 anchor-owned rendering layer，不参与前端几何真源。

---

## 1. 对 MASt3R / DROID / DSO / GSO 的判决

| 模块 | 主线角色 | 理由 |
|---|---|---|
| **MASt3R** | ❌ 从主线删除 | 点云无信号可优化，conf 不是可优化残差，off-ray hallucination 难审计 |
| **DROID** | ❌ 不进主线 | 主状态是 keyframe-window dense depth，和 anchor skeleton 冲突；只做 upper-bound ablation |
| **GSO** | ❌ 不前当前前端 | VO-GS 强耦合会让 GS residual 污染前端；等 anchor-owned GS 成熟后再借鉴调度 |
| **DSO** | ✅ 作为 Anchor-DSO 的 residual/logic base | 但不是原版 DSO；优化对象从 keyframe inverse-depth points 改成 mature anchors |
| **DPVO** | ✅ 第二层 medium-rate proposal | sparse patch BA 比 dense DROID 更接近 anchor 体系 |

---

## 2. 最终前端架构（三环）

```
Fast loop / every frame:
  Anchor-DSO
  用 mature anchors 做稀疏直接优化，估计当前帧 pose

Medium loop / selected frames:
  DPVO-style sparse patch BA
  给局部窗口提供 pose-depth-track proposal

Slow loop / rare:
  MASt3R
  新区域 bootstrap / wide-baseline / loop / relocalization / 强几何审计
```

---

## 3. 最终 pipeline

```
RGB frame I_t
  ↓
DepthPriorNet (Omnidata / HI-SLAM2 baseline)
  D_t, C_t, N_t
  ↓
Anchor Direct Tracker
  mature anchors → 优化当前帧 pose ξ_t
  ↓
ACG / Anchor posterior
  existing anchor / new anchor / transient / outlier / pose error
  ↓
Sliding-window Anchor BA
  poses + candidate anchors + selected active anchors
  (不优化 dense depth，depth map 只是 unary prior)
  ↓
Anchor maturity manager
  proposal → candidate → active → mature → frozen
  ↓
Anchor-submap manager
  submap 基于 anchor coverage 触发，不是 keyframe count
  ↓
Anchor-owned ChildGS mapper
  普通 Gaussian 是 anchor 的 children，不是几何权威
  child Gaussian 不能移动 parent anchor
  child Gaussian 不能写回 VideoBuffer
  render residual 不能成为前端几何真源
```

---

## 4. Anchor 的三重角色

```
1. SLAM landmark:
   用于 pose/depth/gauge residual
2. GS owner:
   管理 child Gaussians 的生成、裁剪、LOD、显存生命周期
3. Deformation handle:
   loop closure / local drift correction 时作为 map warp graph node
```

与 Scaffold-GS / SEGS-SLAM 的关键区别：
- 它们：anchor = rendering scaffold
- 我们：anchor = persistent SLAM state，child GS 是 downstream appearance/detail emission

---

## 5. Depth/Normal Prior 纪律

默认选择：Omnidata / HI-SLAM2 baseline（表格综合最优）。
备选：MoGe-2（normal/detail）、UniDepthV2（confidence weight）。
暂不默认：Depth Anything 3。

四条纪律：

1. **必须 scale-align**
   - 沿用 HI-SLAM2 grid-based scale alignment
   - log D_aligned(u) = log D_raw(u) + α_g(u)

2. **Prior weight 随 maturity 衰减**
   - proposal: strong
   - candidate: medium
   - active: weak
   - mature/frozen: validation only

3. **Normal 只约束局部朝向，不约束全局位置**
   - 初始化 anisotropic covariance
   - 不决定 anchor center / pose update

4. **Depth/normal prior 不直接喂给 GS**
   - depth prior → candidate anchor → multi-frame verification → mature anchor → child GS

---

## 6. 禁止 / 仍为假

```
certified_packet_materialized        = false
gs_facing_packet_allowed              = false
gs_consumption_allowed                = false
video_buffer_writeback_allowed         = false
anchor_mutation_allowed               = false
dpvo_authority_allowed                 = false
MASt3R as main frontend               = false
full DROID as main frontend           = false
GSO as main frontend                  = false
GS render loss writes pose/depth/anchors = false
child Gaussian moves anchor           = false
```

---

## 7. 实验验证计划

### A: anchor stability probe
- GT pose + VideoBuffer depth prior + Omnidata
- 比较：voxel anchor vs MASt3R threshold vs MDL covariance-surfel
- 指标：survival rate / support count / repeatability / NLL / dynamic contamination

### B: frontend replacement probe
- 同一 AnchorGraph，换 observation source
- 证明系统是 frontend-agnostic

### C: GS-before / GS-after geometry validation
- 比较 vanilla GS vs 你的 persistent anchors + child GS
- 重点不是 PSNR 最高，而是同质量更少 Gaussian、更少 pose-error absorption

### D: pose/depth perturbation recovery
- Build mature anchors → freeze → inject perturbation → ACG transaction → recover
- 验证系统能否区分 pose error / depth gauge / anchor update / dynamic

---

## 8. 执行顺序

```
Step 0: 冻结 MASt3R 改造，不再投入
Step 1: 实现 Anchor-DSO 主前端（pose-only tracking over mature anchors）
Step 2: 做 candidate anchor birth（从 depth prior + multi-frame verification）
Step 3: 做 sliding-window Anchor BA（替代 DROID dense DBA）
Step 4: 接 anchor-owned ChildGS mapper
Step 5: MASt3R 只做 posterior-scheduled oracle
Step 6: DROID 只做 upper-bound ablation
Step 7: GSO-like coupling 进入 anchor-owned GS mapper（非前端）
```

---

## 9. 相关决策日志

- [[decision-log|SkelGS-SLAM Decision Log]] — 见 2026-06-05 / 06-08 相关条目
- [[progress-record-2026-06-08]] — 项目进展记录

---

## 10. 状态

- [x] 2026-06-05: Anchor-DSO synthetic preflight passed, route_verdict = anchor_dso_pose_certificate_rejected_dso_kernel_retained
- [x] 2026-06-05: AnchorGenerationAudit synthetic gate passed
- [x] 2026-06-08: GPT Pro 战略讨论确认方向
- [ ] Step 1: pose-only Anchor-DSO over mature anchors
- [ ] Step 2: candidate anchor birth from depth prior
- [ ] Step 3: sliding-window Anchor BA
- [ ] Step 4: anchor-owned child GS
