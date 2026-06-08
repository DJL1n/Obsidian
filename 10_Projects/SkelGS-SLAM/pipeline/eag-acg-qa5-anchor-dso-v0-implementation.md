# EAG / ACG QA5: Anchor-DSO v0 Implementation Verdict

Date: 2026-06-04
Status: fixed decision record.

---

## 0. v0 Target

Implement:

```text
Synthetic / GT-pose FrozenMatureAnchorFixture
  -> photometric residual preflight
  -> pose-only Anchor-DSO over fixed anchors
  -> diagnostics only
```

v0 explicitly excludes:
- online AnchorGraph / old UGG admission / old ACG posterior update
- anchor geometry mutation / depth/gauge writeback
- CertifiedPacket mutation / ChildGS / GS residual

---

## 1. 实现顺序

```
1. Coordinate convention tests
2. synthetic_corner_v0 renderer
3. FrozenMatureAnchorFixture builder
4. Tangent-plane patch extraction
5. Residual evaluator
6. Preflight residual landscape（GT vs perturb vs shuffled vs random）
7. Only if preflight passes: finite-difference LM pose-only solver
8. Perturbation recovery summary
```

禁止在 steps 1–7 通过前触碰 HI-SLAM2 adapter / AnchorGraph / ACG / ChildGS。

---

## 2. 坐标约定

```
T_cw: scene/map frame S -> camera frame C
T_cw_new = Exp_se3(delta_xi) @ T_cw_current
OpenCV camera axes: +Z forward, +X right, +Y down

投影: u_x = fx * X_c.x / X_c.z + cx；u_y = fy * X_c.y / X_c.z + cy
Camera center: C_s = -R_cw^T t_cw
Backprojection: x_c = D_ref(u) * K^-1 [u, v, 1]^T
```

---

## 3. Synthetic Fixture

**synthetic_corner_v0**: 两块垂直纹理平面

```
Camera: W=640, H=480, fx=450, fy=450, cx=319.5, cy=239.5
Front wall: z=3.0, x∈[-2.0,2.0], y∈[-1.3,1.3], normal=[0,0,-1]
Right wall: x=1.5, z∈[1.0,3.8], y∈[-1.3,1.3], normal=[-1,0,0]
Texture: 0.50 + 0.20 sin(11.3u) + 0.15 sin(17.7v) + 0.10 sin(9.1u+4.7v) + 0.05 hash_noise, clamp [0,1]

Frame 0: C_s=[0,0,0], R_cw=I
Frame 1: C_s=[0.02,0,0], yaw=1°
Frame 2: C_s=[0.05,0.01,0], yaw=3°, pitch=-1°
Frame 3: C_s=[0.10,0.02,0.02], yaw=5°, pitch=-2°
```

---

## 4. Anchor 采样

```
image: grayscale float32 [0,1]
grid cell_size = 24 px
max 1 candidate per cell
ignore 8 px border
depth 0.2m–20.0m
5x5 depth range ≤ 0.05
5x5 normal angular spread ≤ 15°
9x9 structure tensor texture score = λ_min(G)
per cell choose highest λ_min(G), require ≥ global P60
```

### Fixture-only repeatability audit
```
visible in ≥ 2 validation frames
valid projected sample ratio ≥ 0.6
GT-pose patch mean_abs residual ≤ 0.08
status = mature_frozen
provenance = fixture_gt_oracle
```

---

## 5. Anchor Patch 表示

使用 tangent-plane surfel patch，非普通方形 patch。

```
5 × 5 pattern, K = 25
dx, dy ∈ {-2, -1, 0, 1, 2}

Patch sample point:
  q = [u+dx, v+dy, 1]
  ray_c = K^-1 q
  ray_s = R_cw_ref^T ray_c
  C_s = -R_cw_ref^T t_cw_ref
  λ = dot(n_s, μ_s - C_s) / dot(n_s, ray_s)
  p_jk_s = C_s + λ * ray_s
```

### Train / heldout split
```
train anchors: anchor_id % 5 != 0
heldout anchors: anchor_id % 5 == 0
train samples: checkerboard even
heldout samples: checkerboard odd
```

---

## 6. Pose-Only Residual

```
r_jk = I_t(u_jk_t) - exp(alpha_t) * I_ref_jk - beta_t
```

Synthetic v0: optimize_brightness = false（alpha=0, beta=0）
Real fixture later: alpha, beta 允许但强正则化。

Robust kernel: Huber delta = 0.05（intensity [0,1]）

Preflight metrics:
- **primary gate**: heldout mean_abs residual
- **secondary sanity**: heldout Huber energy（如 mean_abs 失败但 Huber 通过 → preflight 失败）

---

## 7. Solver（第二 commit）

```
Levenberg-Marquardt, finite-difference Jacobian
Variables: [tx, ty, tz, rx, ry, rz] → later + [alpha, beta]
SE(3): T_cw_new = Exp_se3(delta_xi) @ T_cw_current
LM: max_iters=15, λ_init=1e-3, λ_up=10.0, λ_down=0.1
Stop: relative energy decrease < 1e-4
      or trans delta < 1e-6 × median_depth
      and rot delta < 1e-6 rad

Degenerate if:
  rank < 6 or condition > 1e8
  or visible anchors < 50
  or valid train samples < 1000
```

---

## 8. 硬通过门限

| Gate | 条件 |
|---|---|
| G1 | rot error ↓ ≥ 60%, trans error ↓ ≥ 50% |
| G2 absolute | 按 perturbation 等级设定 final rot/trans 上限 |
| G3 heldout | heldout photometric residual 改善 ≥ 5% |
| G4 Hessian | rank=6, condition ≤ 1e8 |
| G5 controls | shuffled-template / random-geometry / no-op 全部失败 |
| G6 | pass ≥ 80% non-degenerate cases |

Failure → 不进入 HI-SLAM2 adapter / online anchor audit / ACG / ChildGS。

---

## 9. 前检（Preflight Hard Stop）

在 solver 实现前，评估：

```
T_gt
T_perturb_1deg_2cm / _3deg_5cm / _5deg_10cm
shuffled-template control
random-geometry control
```

Synthetic corner 要求：
- 1deg/2cm: E_gt_heldout ≤ 0.97 × E_perturb_heldout
- 3deg/5cm: ≤ 0.90
- 5deg/10cm: ≤ 0.90
- controls: E_gt_heldout < E_shuffled < E_random

失败 → status = invalid_residual_landscape → fix convention/projection/patch/brightness。

---

## 10. First Commit Deliverables

```
src/anchor_dso/types.py
src/anchor_dso/synthetic_corner.py
src/anchor_dso/fixture_builder.py
src/anchor_dso/residual_eval.py
src/anchor_dso/controls.py
tests/anchor_dso/test_coordinate_convention.py
tests/anchor_dso/test_fixture_preflight.py
```

Outputs:
```
frozen_anchor_map.json
frozen_anchor_patches.npz
anchor_visibility.json
fixture_preflight.json
control_summary.json
```

---

## 11. Mandatory Controls

- no-op
- GT upper bound
- shuffled-template（保留 geometry/visibility/depth/normal marginals，permute patch templates）
- random-geometry（保留 patch templates，替换 geometry）
- later: same-DOF 2D affine image warp control

Controls must not pass the same acceptance gate as real anchors.

---

## 12. v0 禁止调用

Codex 不得实现或调用：
- UGG ready-cell active admission
- old ACG posterior update
- anchor mean/covariance/normal update
- anchor birth/deletion/merge/split
- packet mutation from anchors
- VideoBuffer / CertifiedPacket writeback
- GS render residual / ChildGS creation
- MASt3R pointmap anchor source
- DROID / DPVO track proposal
- PDC-S0 / PDC-O2 optimizer as admitted optimizer

Invariant: **Anchors are fixed. Only current pose moves. Everything else is read-only.**
