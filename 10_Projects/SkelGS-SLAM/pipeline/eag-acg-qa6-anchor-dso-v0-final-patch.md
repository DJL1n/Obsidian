# EAG / ACG QA6: Anchor-DSO v0 Final Patch-Level Verdict

Date: 2026-06-04
Status: fixed decision record.

---

## 0. Final Verdict

Proceed with Anchor-DSO v0 implementation, with two corrections:

```text
1. Preflight gates on heldout mean_abs residual, not Huber.
2. Tangent-plane patch samples must be built by reference ray-plane
   intersection, not by the simple depth/fx and depth/fy approximation.
```

v0 target remains:

```text
Synthetic / GT-pose FrozenMatureAnchorFixture
  -> photometric residual preflight
  -> pose-only Anchor-DSO over fixed anchors
  -> diagnostics only
```

---

## 1. 坐标约定

```
T_cw: scene/map frame S -> camera frame C
X_c = R_cw X_s + t_cw
T_cw_new = Exp_se3(delta_xi) @ T_cw_current

Camera center: C_s = -R_cw^T t_cw
Pose from camera center: t_cw = -R_cw C_s
Projection: u = K X_c / X_c.z, valid iff X_c.z > 0
Perturbation: T_init = Exp_se3(delta_xi) @ T_gt
```

OpenCV-style：+Z forward, +X right, +Y down。`T_cw = I` for frame 0。

Required smoke tests:
- `T_cw = I, [0,0,2]` 投影到 `[cx, cy]`
- backproject/project roundtrip
- 相机右移 → fixed world point 左移
- normal faces camera after flip

---

## 2. 前检 Photometric Residual Preflight

### 指标
```
primary gate:    heldout mean_abs residual
secondary sanity: heldout Huber energy（必须 show same ordering）
```

如果 mean_abs 失败但 Huber 通过 → preflight 失败。

### 阈值
```
1deg/2cm:  E_gt_abs ≤ 0.97 × E_perturb_abs
3deg/5cm:  E_gt_abs ≤ 0.90 × E_perturb_abs
5deg/10cm: E_gt_abs ≤ 0.90 × E_perturb_abs
secondary: E_gt_huber < E_perturb_huber
```

### Controls
```
E_gt_abs < E_shuffled_template
E_gt_abs < E_random_geometry
```

---

## 3. Tangent-Plane Patch Spacing

**禁止**使用简单深度近似作为主要方法：

```text
p_jk_s = mu_j_s + tangent1_s * dx * depth/fx + tangent2_s * dy * depth/fy
```

**必须**使用参考帧 ray-plane intersection：

```text
q = [u+dx, v+dy, 1]
ray_c = K^-1 q
ray_s = R_cw_ref^T ray_c
C_s = -R_cw_ref^T t_cw_ref
λ = dot(n_s, mu_s - C_s) / dot(n_s, ray_s)
p_jk_s = C_s + λ * ray_s
```

Validity: `abs(dot(n_s, ray_s)) > 1e-6`, `λ > 0`, project inside image。

---

## 4. Anchor 纹理阈值

```
texture score ≥ global P60（not P70, not P50）
```

Diagnostic fallback：若 frozen anchors < 200 或 visible/帧 < 50，可暂时 P50 调试，但不作为默认 pass。

Repeatability audit：
```
visible in ≥ 2 validation frames
valid projected sample ratio ≥ 0.6
mean_abs photometric residual at GT pose ≤ 0.08
Report ZNCC, but do not use as primary gate.
Do not use "mean_abs ≤ 0.08 OR ZNCC ≥ 0.35".
```

---

## 5. Controls

First commit：
- no-op / GT upper bound / shuffled-template / random-geometry

Second commit 前（claim pass 前）：
- same-DOF 2D affine image warp control

---

## 6. SE(3) 实现

```
Create anchor_dso/se3.py as v0 convention wrapper.
Either call existing SE3 utilities if they pass convention tests,
or implement Exp_so3 / Exp_se3 manually.
```

Required tests：
- Exp(0) @ T == T
- 正向 tx 在 left update 下表现正确
- finite-difference Jacobian 符号稳定
- `T_cw_new = Exp(delta) @ T_cw_current` 已验证

---

## 7. Hard Stop Before LM

确认。Residual landscape 通过前，不做 LM solver。

```json
{"status": "invalid_residual_landscape"}
```

Fix only：convention / projection / patch construction / bilinear sampler / normal orientation / visibility / texture / intensity normalization。
不通过 LM 补偿 bad preflight。

---

## 8. 输出格式

```
outputs/anchor_dso_v0/synthetic_corner_v0/<run_id>/
```

禁止使用 `certified_pose` / `packet_writeback` / `geometry_commit` / `anchor_update` / `gs_update` 等名称。

只使用 diagnostic 名称：`pose_candidate` / `diagnostic_accept` / `preflight_status` / `control_status`。

---

## 9. Final Implementation Order

```
1. Coordinate convention tests
2. synthetic_corner_v0 renderer
3. FrozenMatureAnchorFixture builder
4. Tangent-plane patch extraction by ray-plane intersection
5. Residual evaluator
6. Preflight residual landscape: GT vs perturb vs shuffled vs random
7. Only if preflight passes: finite-difference LM pose-only solver
8. Perturbation recovery summary
```

Steps 1–7 在 synthetic 通过前，不碰 HI-SLAM2 adapter / AnchorGraph / ACG / ChildGS。
