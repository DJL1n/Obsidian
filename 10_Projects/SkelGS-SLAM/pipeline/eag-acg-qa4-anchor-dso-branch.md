# EAG / ACG QA4: Anchor-DSO Gated Research Branch Decision

Date: 2026-06-04
Status: fixed decision record.

---

## 0. Summary Verdict

Research mainline changes, but the GS-facing engineering boundary does not.

New research branch:

```text
Depth / Normal Prior
  -> Frozen mature-anchor fixture
  -> pose-only Anchor-DSO
  -> CertifiedGeometryPacket
  -> GS
```

Do not immediately switch to full AnchorGraph / ACG / ChildGS.

---

## 1. Frozen Old Routes

### UGG Ready-Cell Active Admission — Forbidden
成熟 anchor 必须来自 fixture / oracle / offline bootstrap，不是 online UGG。

### Old ACG Posterior Update — Forbidden
AnchorAudit / AnchorTransaction 必须从 no-writeback diagnostic 开始。

### Anchor Geometry Mutation — Forbidden in v0
v0 中 frozen anchor mean / normal / covariance / reference patch 全部固定。

### Packet Mutation From Anchors — Forbidden
Anchor-DSO 只发射 candidate pose packet，不直接 mutate CertifiedPacket。

### PDC-S0 / PDC-O2 / WCE Optimizers — Diagnostic only

---

## 2. 执行顺序

```
Stage 0: 分支 + 围栏（anchor_dso_probe，不 import 旧 UGG）
Stage 1: FrozenMatureAnchorFixture（synthetic / GT-pose）
Stage 2: Pose-Only Anchor-DSO Solver（固定 anchors 上的 sparse direct patch residual）
Stage 3: HI-SLAM2 / Omnidata Adapter（只在 Stage 2 通过后）
Stage 4: Real Frozen-Anchor Anchor-DSO
Stage 5: CertifiedPacket Integration
Stage 6: No-Writeback Candidate Anchor Audit
Stage 7: Limited AnchorGraph
Stage 8: Anchor-Owned ChildGS
```

---

## 3. Anchor-DSO v0 变量与约束

### 允许优化的变量
- xi_t（当前帧 SE(3) pose increment）
- alpha_t, beta_t（affine brightness）

### 固定变量
- mature anchor geometry / normal / covariance / reference patch
- depth prior / normal prior
- GS map / VideoBuffer / CertifiedPacket input

### 禁止
- anchor birth/death/merge/split
- anchor geometry update / depth writeback / gauge writeback
- packet writeback / GS residual / ChildGS creation
- ACG posterior commit

---

## 4. 主要残差

Sparse direct photometric patch residual：

```text
r_photo(j,k) =
  I_t(pi(K T_CW_t (mu_j + R_j delta_jk)))
  - exp(alpha_t) I_ref_j(k)
  - beta_t
```

Gate order（字典序）：
1. visibility / texture pre-gate
2. Hessian degeneracy gate
3. training photometric decrease
4. heldout photometric decrease
5. pose recovery / pose improvement
6. shuffled / random anchor rejection

---

## 5. Mandatory Controls

- no-op baseline
- GT-pose upper bound
- shuffled-anchor control
- random-anchor control
- same-DOF local control
- low-texture denominator
- dynamic / occlusion dustbin statistics
- train / heldout split

---

## 6. 硬通过门限

| Gate | 条件 |
|---|---|
| G1 pose improvement | rot error ↓ ≥ 60%, trans error ↓ ≥ 50% |
| G2 absolute final error | 依 perturbation 等级定阈值 |
| G3 heldout residual | heldout photometric residual 改善 ≥ 5% |
| G4 Hessian | rank ≥ 6, condition ≤ 1e8 |
| G5 controls | shuffled / random fail |
| G6 false accept | ≤ 5% |
| G7 success rate | ≥ 80% non-degenerate cases |

### 硬失败 / 杀死条件

| Kill | 条件 |
|---|---|
| K1 | synthetic fixture convention/brightness bug 修复后仍失败 |
| K2 | GT-pose real fixture 无法恢复 1 deg / 2cm perturbation |
| K3 | shuffled / random anchors pass > 5% |
| K4 | training residual 改善但 heldout 不改善 |
| K5 | Hessian 退化但 solver 接受 |
| K6 | 只靠 depth-prior residual 才改善 |
| K7 | 需要更新 anchor geometry 才能通过 v0 |

---

## 7. Depth/Normal Prior

默认：Omnidata / HI-SLAM2 baseline
备选：MoGe-2, UniDepthV2
不默认：Depth Anything 3

---

## 8. MASt3R 角色

从主线删除。只允许：
- appendix oracle
- audit baseline
- wide-baseline / relocalization ablation
- failure-case diagnostic

禁止：
- anchor source of truth
- pointcloud source of truth
- every-frame frontend
- CertifiedPacket producer

---

## 9. 与 ScaffoldGS / ContextGS / SEGS-SLAM 的区别

```
Scaffold/SEGS: anchor = rendering scaffold
本系统:       anchor = persistent SLAM state
              child GS = downstream appearance/detail emission
              AnchorGraph 拥有几何权威
              ChildGS 拥有 appearance/detail 权威
              CertifiedPacket 是唯一 GS-facing geometry bridge
```

---

## 10. ChildGS Later Contract

```text
owner_anchor_id
local_offset (bounded)
local_scale (bounded)
local_rotation (bounded)
color / SH
opacity
World: mu_g = mu_anchor + R_anchor delta_g
```

禁止：free-floating child / child 移动 parent anchor / render loss 写 pose。

---

## 11. 直接指令

先实现：
1. synthetic / GT-pose FrozenMatureAnchorFixture
2. pose-only Anchor-DSO over fixed mature anchors

不要先实现：
- HI-SLAM2 adapter / online AnchorGraph / old ACG
- UGG admission / ChildGS / MASt3R mainline
- DROID / DPVO mainline / GS residual / packet mutation

如果前两步不过，kill anchor route as mainline candidate，回到：

```text
HI-SLAM2 / stronger frontend packet producer -> CertifiedPacket -> GS
```
