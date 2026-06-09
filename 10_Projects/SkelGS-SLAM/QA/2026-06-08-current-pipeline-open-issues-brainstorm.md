# Current Pipeline And Open Issues — QA Brainstorm Record

Date: 2026-06-08

Source: Codex chat QA record.

Status: recorded for later review.

Important note:

This note records the user-provided context document and the assistant's brainstorming answer. It is not itself a project decision log. Any decision should be copied later into `decision-log.md` with evidence, risks, and validation status.

---

## User-Provided File

# Current Pipeline And Open Issues

Date: 2026-06-08

Purpose:

```text
This document summarizes the current SkelGS-SLAM research / engineering
pipeline from pipeline/VeryImportant.md and CURRENT_STATE.md, including the
active route, frozen routes, implementation evidence, known failures, and
questions to ask Obsidian Codex / GPT Pro.
```

Important boundary:

```text
This is a summary document only.
It does not admit new authority.
It does not materialize CertifiedPacket.
It does not enable GS consumption.
It does not allow VideoBuffer writeback.
It does not allow online anchor birth.
```

### 1. One-Sentence Current Pipeline

Current intended route:

```text
HI-SLAM2 / frontend packet producer
  + OnlineAnchorLifecycleLite read-only support
  + depth / normal prior packets
  + anchor lifecycle evidence
  -> read-only CertifiedGeometryPacket candidate
  -> reviewed Certified / CoVersionedGeometryPacket only if explicitly admitted
  -> fixed GS consumption smoke only after certification
```

Current anchor-side runtime direction:

```text
OfflineAnchorLifecycleProof
  -> validates identity / controls / lifecycle policy offline

OnlineAnchorLifecycleLite
  -> bounded-cost runtime-style support summary
  -> coverage / observability trigger
  -> targeted top-up
  -> component-neighborhood lookup
  -> projection evidence cache
  -> still read-only
```

Current important negative result:

```text
Cached online-lite support can gate a read-only HI-SLAM2 packet candidate, but
live projection of selected lifecycle anchors into the current HI-SLAM2
keyframe pose/depth stream currently fails unscaled depth agreement. A single
diagnostic scale explains the mismatch, suggesting scale / depth convention
misalignment rather than random projection failure.
```

### 2. Hard Invariants

These remain non-negotiable:

```text
VideoBuffer remains the persistent frontend source of truth.
GS cannot silently overwrite VideoBuffer pose / depth / intrinsics / gauge /
normals / confidence / geometry weights.
Candidate geometry packets are evaluator/materialization artifacts unless
explicitly committed by a reviewed path.
Certified / CoVersionedGeometryPacket is the only allowed GS-facing geometry
contract.
```

Forbidden unless explicitly reopened:

```text
GS render loss writes pose/depth/anchors
DPVO pose writes VideoBuffer
DPVO pose becomes CertifiedPacket directly
anchor residual mutates CertifiedPacket
online anchor birth
AnchorGraph commit
old UGG active admission
old ACG posterior mutation
ChildGS
MASt3R as mainline geometry truth
DROID dense state as AnchorGraph truth
confidence weighting as primary geometry selector
PDC-GP optimizer / candidate / GS path without read-only gates passing
```

### 3. High-Level Route Status

#### Active Engineering Route

Active route:

```text
AnchorLifecycleLite
  + HI-SLAM2 frontend trajectory / depth / normal packet producer
  -> read-only CertifiedGeometryPacket candidate
```

Why this route is active:

```text
1. HI-SLAM2 frontend keyframe packet can export nontrivial pose, depth, normal,
   intrinsics, and frame ids after warmup.
2. Candidate builder can verify pose / depth / normal / lifecycle support.
3. Online-lite replay can provide bounded-cost support summary instead of the
   expensive full lifecycle proof harness.
4. This keeps all authority boundaries intact.
```

#### Active Research Direction

Anchors remain the core system identity:

```text
Anchor lifecycle manages persistent support, coverage, observability, duplicate
pressure, and later possible anchor-owned GS organization.
```

But current anchors are not yet:

```text
online-born
committed mature AnchorGraph state
GS owners
VideoBuffer writers
CertifiedPacket mutators
```

#### Frozen / Demoted Routes

```text
Anchor-DSO direct-only pose certificate: rejected.
DSO-style direct kernel: retained only as possible refinement/diagnostic.
Anchor-PnP: candidate_producer_only, not admitted.
DPVO free-running anchor accelerator: rejected/frozen.
DPVO segmented pre-certification: frozen.
DPVO local untrusted PoseCandidate: allowed.
PDC-GP-O2: read-only diagnostic only.
MASt3R: appendix / oracle / audit only.
HI-SLAM2/DROID: bootstrap / fallback / baseline, not permanent anchor truth.
```

### 4. Current Packet Pipeline In Detail

#### 4.1 Frontend Packet Producer

Current practical producer:

```text
src/frontend/hislam2/demo.py
src/frontend/hislam2/frontend_runner.py
src/frontend/hislam2_packet_export.py
```

Local HI-SLAM2 frontend export can produce:

```text
images
pose7_tcw_xyzw / pose7_twc_xyzw
traj_kf_pre_backend.txt
intrinsics.npy
intrinsics_per_kf.npy
depths.npy
depths_prior.npy
normals.npy
tstamps.npy
```

Important room0 len120 export:

```text
output:
  outputs/hislam2_frontend_room0_len120_packet_20260607

keyframes:
  [0, 18, 30, 44, 51, 62, 68, 88, 103, 113, 119]

trajectory:
  nontrivial
  camera_center_translation_span_m = 0.840824764485906
  rotation_span_deg = 30.501318393179044

depth/normal:
  depths.npy finite
  depths_prior.npy finite
  normals.npy finite
```

#### 4.2 Read-Only Candidate Builder

Implemented:

```text
src/frontend/hislam2_certified_packet.py
scripts/build_hislam2_certified_geometry_packet_candidate.py
tests/test_hislam2_certified_packet.py
```

Candidate builder checks:

```text
trajectory finite / nontrivial
rotation matrices valid
intrinsics valid
depth arrays finite / positive
normal arrays finite / approximately unit norm
depth/normal frame ids cover trajectory frame ids
lifecycle support present and passed
authority flags remain read-only
```

Positive room0 candidate:

```text
output:
  outputs/hislam2_certified_packet/replica_room0_hislam2_frontend_len120_online_lite_candidate_ready_20260608.json

status:
  certification_candidate_ready

certification_ready:
  true

blocked_reasons:
  []

lifecycle support source:
  online_lite_replay_summary
```

Authority still forbids:

```text
certified_packet_materialized = false
gs_facing_packet_allowed = false
gs_consumption_allowed = false
video_buffer_writeback_allowed = false
anchor_mutation_allowed = false
```

Meaning:

```text
We have a read-only candidate manifest that satisfies the current checks.
We do not yet have a materialized CertifiedGeometryPacket.
We do not yet have GS fixed-consumption.
```

### 5. Anchor Lifecycle Pipeline

#### 5.1 OfflineAnchorLifecycleProof

Purpose:

```text
Validate whether candidate anchors can be trusted under temporal visual /
geometric support, diversity, observability, duplicate identity controls, and
negative controls.
```

Inputs:

```text
RGB frames
K
T_cw sequence
depth
normal
optional synthetic oracle labels
```

Important outputs:

```text
anchor_candidate_table.jsonl
temporal_trust_candidate_table.jsonl
anchor_pair_association_table.jsonl
anchor_component_table.jsonl
generation_budget_summary.json
association_control_summary.json
soft_duplicate_summary.json
lifecycle_timing_table.json
```

Hard meaning:

```text
This is proof / regression / diagnostics.
It is not runtime.
It is not anchor birth.
It is not AnchorGraph commit.
```

#### 5.2 AnchorGenerationAudit Results

Synthetic corner v0:

```text
num_candidates = 454
visual_pass_anchor_count = 429
trusted selected anchors = 344
hard_gate_pass = true
route_verdict = anchor_generation_audit_passed
```

Key finding:

```text
Visual temporal trust alone over-concentrated anchors on front wall.
Normal-bucket diversity selection was required.
```

Replica GT-pose/depth positive windows:

```text
room0 / office1 / office2 all passed first read-only generation audits.
```

Still not admitted:

```text
online anchor birth
mature admission
AnchorGraph commit
GS consumption
CertifiedPacket mutation
```

#### 5.3 AnchorLifecycleAudit Results

Synthetic:

```text
synthetic_corner_v0 4-frame fixture:
  lifecycle_hard_gate_pass = true

synthetic_corner_long_v0:
  initially failed fixed schedules due coverage / normal-bucket balance
  local-refill adaptive selection fixed budget
  strict merge remained conservative
  SOFT_DUPLICATE_LINK recovered duplicate evidence after negative-appearance
  guard
```

Replica:

```text
room0 / office1 / office2 small-window cross-scene lifecycle passed.
dense stride4 diagnostics passed across room0 / office1 / office2.
stride1 count60 adaptive-only cap=150 passed all three scenes under 45s.
```

Important runtime-like behavior:

```text
adaptive schedule does not generate every frame.
It rapidly refills early support, then refreshes at max interval / viewpoint
change.
```

#### 5.4 OnlineAnchorLifecycleLite

Purpose:

```text
Bounded-cost runtime-style support summary, not proof harness.
```

Implemented:

```text
scripts/replay_online_anchor_lifecycle_lite.py
src/anchor_audit/online_lite_state.py
src/anchor_audit/online_lite_topup.py
src/anchor_audit/online_lite_cache.py
src/anchor_audit/online_lite_components.py
src/anchor_audit/online_lite_projection_cache.py
src/anchor_audit/online_lite_live_projection.py
```

Online-lite replay room0:

```text
base = keyframes + stride4
reference = keyframes + stride2
unique_components_base = 95
target_unique_components = 150
bounded overflow top-up:
  topup_ref_count = 11
  topup_candidate_count = 57
  unique_components_after_topup = 152
online_lite_pass = true
runtime_support_pass = true
proof_equivalent_pass = true
```

Cross-scene cached replay:

```text
office1 dense_stride4:
  unique_components = 297
  topup = 0
  pass = true

office2 dense_stride4:
  unique_components = 299
  topup = 0
  pass = true
```

Component-neighborhood lookup:

```text
existing_component_count = 106
proposal_count = 57
all_pair_count = 6042
neighborhood_pair_count = 153
pair_reduction_ratio = 39.490196078431374
component_merge_allowed = false
anchor_birth_allowed = false
anchor_mutation_allowed = false
```

Projection evidence cache:

```text
key:
  anchor_instance_id
  target_frame_id
  pose_version
  image_version
  depth_version
  patch_schema_version

stores:
  center_uv
  valid_center
  valid_sample_ratio
  mean_abs
  zncc
  depth_relative_error
  view_cos
  normal_consistency
  occlusion_flag
  outlier_ratio
  H_pose_trace_approx
  H_translation_trace_approx
```

Current caveat:

```text
Most online-lite replay evidence is still cached from audit rows.
Live projection has started, but live photometric patch sampling is not
implemented yet.
```

### 6. Live Projection / Scale Problem

This is the newest and most important open problem.

Live projection smoke:

```text
script:
  scripts/run_online_lite_live_projection_smoke.py

candidate table:
  outputs/anchor_lifecycle_audit/replica_room0/hislam2_frontend_len120_stride4_plus_keyframes_20260607/adaptive_schedule_summary.json

trajectory/depth/intrinsics:
  outputs/hislam2_frontend_room0_len120_packet_20260607
```

Result with T_wc input interpreted as `twc`:

```text
status = diagnostic_completed
support_status = depth_agreement_failed
shared_frame_count = 10
candidate_count_total = 35
valid_center_count = 35
valid_center_ratio = 1.0
depth_checked_count = 35
depth_pass_count = 0
support_pass_count = 0
```

Interpretation:

```text
All checked anchors project into the image.
The failure is not image bounds.
The failure is depth agreement.
```

With `depths_prior.npy`:

```text
projected_depth_median = 4.3843760216639724
sampled_depth_median = 1.7818102836608887
projected_to_sampled_depth_median_ratio = 2.460630102917511
depth_relative_error_median = 1.4500475890387232

diagnostic_scale_to_sampled_median = 0.40815533725708175
diagnostic_scaled_depth_relative_error_median = 0.020037711269193965
diagnostic_scaled_depth_pass_count = 35 / 35
```

With `depths.npy`:

```text
projected_depth_median = 4.3843760216639724
sampled_depth_median = 3.186952590942383
projected_to_sampled_depth_median_ratio = 1.375726778655189
depth_relative_error_median = 0.3512888439719949

diagnostic_scale_to_sampled_median = 0.7400342306242889
diagnostic_scaled_depth_relative_error_median = 0.020062902959597714
diagnostic_scaled_depth_pass_count = 35 / 35
```

T_cw vs T_wc check:

```text
tcw interpretation was worse:
  valid_center_count = 13 / 35
  depth_pass_count = 0

twc interpretation projects all 35 / 35 into image.
```

Current conclusion:

```text
The live mismatch looks like a scale / depth convention issue, not random
local projection failure.

A single diagnostic scale factor fixes all checked anchors, but this scale is
diagnostic-only and is not written back.
```

Critical unresolved question:

```text
Should the system introduce a read-only scale-alignment diagnostic / gate
between lifecycle anchor scene frame and HI-SLAM2 keyframe depth, or should
this be treated as a frontend packet convention bug that must be fixed before
any further candidate/GS work?
```

### 7. DPVO Pipeline And Problems

#### 7.1 DPVO Setup

DPVO installed under:

```text
thirdparty/DPVO
commit = 859bbbfdac6c6185f345003b3c473901fcd13ace
env = /home/user/miniconda3/envs/py310
model = thirdparty/DPVO/dpvo.pth
```

GPU note:

```text
nvidia-smi sees RTX 4090.
sandboxed Python may report cuda_available=false.
escalated py310 execution sees cuda_available=true.
Full-res len40 DPVO hit CUDA OOM.
Half-res len40 can run.
```

#### 7.2 DPVO As Anchor Accelerator

Rejected:

```text
stable DPVO track radius filter
shallow DPVO local/cell feature trust predictor
free-running DPVO track as anchor identity / admission
```

Radius filter result:

```text
small radius:
  enough candidate reduction but loses coverage

large radius:
  preserves coverage but not enough reduction

no radius satisfies:
  candidate_reduction >= 3x
  coverage_retained >= 0.90
  rank preserved
```

Trust predictiveness:

```text
best_auc = 0.5979
gate = 0.70
best feature = local_dpvo_weight_mean
nearest track distance / local density / cell support were near random
```

Formal verdict:

```text
free_running_anchor_accelerator_rejected_frozen
```

Allowed DPVO roles:

```text
PoseCandidate source
VO fallback
external comparison baseline
debug signal
lifecycle trigger diagnostic
anchor-conditioned seeded patch probe
local search prior diagnostic
negative evidence diagnostic
```

Forbidden DPVO roles:

```text
anchor identity authority
candidate scoring bonus from current shallow features
supplemental free-track anchorlets
birth suppression
CertifiedGeometryPacket source
GS source
VideoBuffer writeback
```

#### 7.3 DPVO PoseCandidate

DPVO can produce useful local untrusted pose proposals.

Len12 room0:

```text
candidate_count = 12
accepted_diagnostic_count = 7
after GT diagnostic first-frame alignment:
  mean rotation error = 0.0217 deg
  p90 rotation error = 0.0421 deg
  mean translation error = 0.0499 m
  p90 translation error = 0.1064 m
```

Len19 non-GT HI-SLAM2 bootstrap:

```text
shared frames = [0, 18]
mean delta = 0.0177 deg / 0.06525 m
still smoke_only due only two shared frames
```

Len40 half-res room0:

```text
pose_candidate_available_count = 40 / 40
support_rich_pose_candidate_count = 26 / 40
trajectory agreement with HI-SLAM2 on [0,18,30]:
  mean delta = 0.03165 deg / 0.09793 m
temporal_sanity_pass = true
```

But pre-certification failed later:

```text
late anchor/depth agreement failed around frames 32-39
Replica GT showed monotonic translation drift:
  frame0 = 0.0 m
  frame10 = 0.1019 m
  frame20 = 0.1819 m
  frame30 = 0.2590 m
  frame39 approx = 0.3319 m
```

Segmented DPVO rescue:

```text
window_len=20 stride=10:
  segment_pass_count = 2 / 3
  overlap_pass_count = 0 / 2

window_len=16 stride=8:
  segment_pass_count = 2 / 4
  overlap_pass_count = 0 / 3

late segments had insufficient support:
  support ratios around 0.15 / 0.1875 / 0.0
  missing spans 14 / 10 / 16
```

GPT Pro freeze verdict:

```text
Freeze DPVO segmented trajectory -> pre-certification candidate.
Do not freeze DPVO entirely.
```

DPVO remains:

```text
local untrusted PoseCandidate
VO fallback
external baseline
debug / motion prior diagnostic
```

### 8. Anchor-DSO And Anchor-PnP Problems

#### 8.1 Anchor-DSO

Implemented v0:

```text
synthetic_corner_v0
FrozenMatureAnchorFixture
ray-plane tangent patch extraction
GT-vs-perturb preflight
single-scale LM
pyramid LM rescue
same-DOF 2D affine control
```

Result:

```text
preflight passed
pyramid direct residual improved heldout residual
rotation improved
translation did not reliably recover
hard_gate_pass = false
pass_rate = 0.6818 on 45 cases
mean_rot_improvement = 0.8117
mean_trans_improvement = -0.7640
```

Verdict:

```text
Reject direct-only Anchor-DSO as pose certificate.
Do not kill DSO-style direct kernel.
Keep it as possible local refinement / diagnostic.
```

Problem:

```text
Photometric residual decrease is not a pose certificate.
Translation / scale coupling remains weak.
Direct-only tracking can locally repair photometric error without passing
geometry hard gates.
```

#### 8.2 Anchor-PnP / Patch-Track

Implemented:

```text
fixed-anchor patch-track PoseCandidate producer
local ZNCC search
OpenCV PnP-RANSAC
shuffled / random controls
recovery suite
```

Latest observations:

```text
search_step_px=1:
  pass_rate = 0.6667 on small smoke
  still not admitted

coarse search + local refine:
  pass_rate = 0.3333

normal-axis balance:
  pass_rate = 0.0
```

Problem:

```text
Fine patch search helps, but brute force step=1 is too slow.
Coarse peak can lock onto wrong local match.
Need a real coarse-to-fine / gradient / DPVO-guided local search strategy if
this path is reopened.
```

### 9. HI-SLAM2 Problems

HI-SLAM2 positive:

```text
Can produce nontrivial keyframe pose/depth/normal packet after warmup.
Read-only candidate builder can validate it.
Online-lite support summary can gate it.
```

HI-SLAM2 problems:

```text
1. Runtime efficiency:
   full lifecycle count120 took about 104s before online-lite replay.

2. Keyframe-only support too sparse:
   keyframes alone failed unique support / coverage / normal-bucket gates.

3. Short frontend len40 pose was identity:
   candidate correctly blocked as trajectory_has_no_motion_for_multiple_poses.

4. Depth / normal frame coverage:
   short depth-normal arrays cannot certify full trajectory.

5. Live projection scale mismatch:
   lifecycle anchors project into image, but depth consistency fails unless a
   diagnostic scale is applied.

6. HI-SLAM2 as permanent frontend remains undesirable:
   efficiency and backend/global optimization effects are not ideal for a
   high-frequency anchor-centric frontend.
```

### 10. Online-Lite Acceleration Problems

Solved / improved:

```text
full proof harness is no longer treated as runtime.
online-lite replay avoids fixture rebuild.
targeted top-up reaches proof-equivalent support on room0.
office1/office2 dense stride4 pass without top-up.
component-neighborhood lookup reduces pair comparisons about 39.5x on room0.
projection evidence cache is versioned.
live projection updater exists.
```

Still open:

```text
1. Cached replay is not true online runtime.
2. Live projection only checks center/depth, not photometric patches.
3. Live projection currently fails unscaled depth agreement.
4. Component-neighborhood lookup is only representative lookup/statistics,
   not full runtime association policy.
5. Soft duplicate suppression remains diagnostic; not online birth suppression.
6. There is no online anchor commit path.
7. There is no real runtime frame loop connecting HI-SLAM2 stream -> live
   evidence update -> support state -> packet candidate.
```

### 11. PDC-GP-O2 / Hodge Route

Status:

```text
read-only diagnostic only
not immediate implementation target
```

Latest real smoke:

```text
tile field fit executable
blocked_node_grid_read_only_probe
reason = shuffled_graph_ties_or_beats_shared
```

Problem:

```text
Shared node/tile conservative field has not beaten shuffled/random/local/null
controls strongly enough.
Do not tune thresholds to pass.
No optimizer / candidate / GS path admitted.
```

If reopened:

```text
implement LOCAL_DOF_GRID
implement FREE_GRID_SAME_DOF
add cycle / symmetry / ray guards
run larger denominator office1 / office2 / room0
```

### 12. Certified / CoVersionedGeometryPacket Scaffold

Positive scaffold:

```text
Certified / CoVersionedGeometryPacket found and fixed stale GS-facing
side-channel consumption.
geometry_weight epoch/source propagation fixed.
strict matrix passes.
```

Allowed claim:

```text
Certified packet construction prevents stale GS-facing side-channel consumption
and makes P-v5 co-versioned packets safely consumable by GS.
```

Not claimed:

```text
pose-depth consistency solved
active geometry optimizer validated
confidence residual weighting useful
```

Current gap:

```text
The current HI-SLAM2 + online-lite output is still only a read-only candidate.
Reviewed materialization into an actual CertifiedGeometryPacket has not been
implemented in this route.
GS fixed-consumption smoke has not been run.
```

### 13. Current Most Important Problems

#### Problem A: Live Projection Scale / Depth Convention Mismatch

Evidence:

```text
35 / 35 anchors project into image.
0 / 35 pass unscaled depth agreement.
Global diagnostic scale makes 35 / 35 pass.
```

Possible interpretations:

```text
1. lifecycle anchor mu_s scale differs from HI-SLAM2 keyframe depth scale;
2. depths.npy / depths_prior.npy are in different conventions;
3. trajectory scale and depth prior scale are not aligned;
4. candidate anchors come from a scene-frame / depth convention not identical
   to the live packet stream;
5. a read-only scale-alignment gate may be necessary before packet
   certification.
```

Decision needed:

```text
Should we add a read-only global or low-res scale-alignment diagnostic/gate, or
should this be treated as a bug in the frontend packet convention that must be
fixed at the source?
```

#### Problem B: Online-Lite Is Still Cached Replay

Evidence:

```text
online-lite pass results mostly come from cached audit outputs.
live projection path only recently started.
```

Decision needed:

```text
How far must live evidence go before reviewed CertifiedPacket materialization
is allowed?
Is center/depth enough, or must photometric patch evidence be online too?
```

#### Problem C: DPVO Has Useful Pose But Failed Certification Roles

Evidence:

```text
DPVO pose trajectory can align closely to HI-SLAM2 in short windows.
But free-running tracks do not predict anchor trust.
Segmented pre-certification failed overlap/support gates.
```

Decision needed:

```text
Should DPVO remain completely auxiliary, or should we reopen only
anchor-conditioned seeded patch tracking / motion-prior local search?
```

#### Problem D: Anchor-PnP Needs Efficient Accurate Local Matching

Evidence:

```text
search_step_px=1 improves pose recovery but is too slow.
coarse search can lock onto wrong local peak.
```

Decision needed:

```text
Is Anchor-PnP worth reopening with DPVO-guided local motion / differentiable
patch search, or should it remain diagnostic until live scale issue is solved?
```

#### Problem E: No Actual GS Step Yet

Evidence:

```text
candidate_ready exists.
CertifiedPacket materialization is still false.
GS consumption is still false.
```

Decision needed:

```text
When is it safe to run fixed GS consumption smoke?
Before or after live projection scale alignment?
```

#### Problem F: AnchorGraph / ChildGS Still Not Admitted

Evidence:

```text
anchor lifecycle support has strong read-only evidence.
online birth / persistent AnchorGraph commit remains forbidden.
```

Decision needed:

```text
What exact gate graduates from read-only OnlineAnchorLifecycleLite to a
limited read-only AnchorGraph component table, and eventually to owner_anchor_id
metadata for GS?
```

### 14. Suggested Questions For Obsidian Codex / GPT Pro

#### Q1: Scale mismatch

```text
Live projection of lifecycle anchors into HI-SLAM2 keyframes projects all
checked candidates into image, but unscaled depth agreement fails 0/35.
A single diagnostic scale makes 35/35 pass with median depth relative error
around 0.02.

Should the next step be:
  A. treat this as a frontend packet scale/depth convention bug and fix source;
  B. implement a read-only global scale-alignment diagnostic gate;
  C. implement low-res tile/node scale field gate, similar to PDC-GP-O2 but
     only as packet convention alignment;
  D. block all GS/certification work until live unscaled projection passes?
```

#### Q2: Candidate vs CertifiedPacket

```text
HI-SLAM2 + online-lite support produces certification_candidate_ready, but live
projection currently exposes a scale mismatch.

Should CertifiedGeometryPacket materialization remain blocked until live
projection passes unscaled, or can a diagnostic scale gate be part of the
candidate certification contract?
```

#### Q3: Online-lite maturity

```text
Online-lite replay passes cost and support gates. Component-neighborhood lookup
and projection evidence cache are implemented. But true live photometric patch
evidence is not implemented.

What is the minimum online-lite evidence required before GS fixed consumption?
  center/depth projection?
  photometric patch projection?
  depth/normal agreement?
  component-neighborhood duplicate check?
```

#### Q4: DPVO role

```text
DPVO free-running anchor acceleration failed, and segmented pre-certification
is frozen. But DPVO remains useful as local pose proposal.

Should DPVO be reopened only as:
  A. anchor-conditioned seeded patch tracker;
  B. local search prior for Anchor-PnP;
  C. negative evidence / instability detector;
  D. never touch until HI-SLAM2/live-scale issue is solved?
```

#### Q5: Anchor-PnP

```text
Anchor-PnP step=1 search improves but is too expensive. Coarse refine is not
enough.

Should Anchor-PnP be paused, or should we implement a DPVO/motion-prior-guided
local patch search as the next pose candidate kernel?
```

#### Q6: GS timing

```text
The project has a read-only candidate ready and an existing CertifiedPacket
scaffold, but live projection found a scale mismatch.

Should fixed GS smoke wait for:
  A. live scale issue resolved;
  B. read-only scale diagnostic added;
  C. actual CertifiedGeometryPacket materialization path;
  D. multi-scene HI-SLAM2 + online-lite candidate-ready evidence?
```

### 15. My Current Recommended Next Step Before GPT Pro Decision

If no new decision is given, the most defensible next engineering step is:

```text
Do not materialize CertifiedPacket.
Do not run GS.
Do not reopen DPVO/Anchor-PnP.

First diagnose the live projection scale mismatch:
  1. add per-frame scale diagnostics, not just global median;
  2. compare depths.npy vs depths_prior.npy across all shared keyframes;
  3. verify whether lifecycle mu_s was built from depths.npy or depths_prior.npy;
  4. verify whether T_cw trajectory and depth are in the same scale/gauge;
  5. test whether a fixed scalar per sequence or per frame explains all
     candidates robustly;
  6. keep all scale values read-only, no writeback.
```

Reason:

```text
The newest failure is the first true runtime-style mismatch. It directly
affects whether HI-SLAM2 + anchor lifecycle evidence can become a certified
GS-facing geometry packet. Solving or classifying this should happen before
any GS smoke or DPVO reopening.
```

---

## Assistant Answer

### 依据

查阅的 vault 笔记：

- `10_Projects/SkelGS-SLAM/progress-record-2026-06-08.md`
- `10_Projects/SkelGS-SLAM/decision-log.md`
- `40_Knowledge/Concepts/JDSA-Scale-Alignment.md`
- `40_Knowledge/Methods/JDSA-Scale-Alignment-for-CertifiedPacket.md`
- `40_Knowledge/Concepts/Pose-Depth-Co-Versioning.md`
- `40_Knowledge/Concepts/Geometry-Authority-Separation.md`
- `40_Knowledge/Methods/DPVO-Temporal-Frontend.md`
- `40_Knowledge/Methods/Patch-Temporal-Optimization.md`
- `40_Knowledge/Methods/Multi-Evidence-Anchor-Admission.md`
- `40_Knowledge/Concepts/GS-Birth-Gate.md`
- `40_Knowledge/Concepts/Submap-Level-Provenance.md`

`40_Knowledge` 中未查到独立的 PDC/Hodge 笔记，所以这部分主要基于用户提供的当前实验结果判断。另：`decision-log.md` 尾部有标注 `2026-06-09` 的条目；当前系统日期是 2026-06-08，我只把它们作为 vault 已有摘记使用。

### 总判断

当前最该优先解决的是 **scale/depth convention classification**，不是 GS、不是 DPVO、不是 Anchor-PnP。原因很直接：35/35 投影进图，但 0/35 unscaled depth pass，单一 diagnostic scale 能救回来，这说明几何链路不是随机坏，而是 gauge / depth convention / scale alignment 边界没有定义清楚。这个问题不分类，CertifiedPacket 和 GS 都会变成“把错误尺度包装成系统能力”。

### A. Scale Mismatch 建议

1. 先把它当 **frontend packet convention bug** 排查，而不是马上引入认证 scale。认证层可以记录 scale diagnostic，但不能替 source 修 bug。
2. 增加 `ScaleConventionAudit`：检查 `T_wc/T_cw`、depth 是 metric / inverse / normalized / prior-aligned / BA-refined、intrinsics 是否对应 resize/crop 后图像。
3. 同时对 `depths.npy` 和 `depths_prior.npy` 做 per-frame scale：median、MAD、p10/p90、valid ratio，不只看 global median。
4. 做 per-tile / per-normal-bucket / per-depth-bin scale。如果全局 scale 方差低，是 convention/gauge；如果 tile 方差高，是 JDSA-style spatial scale distortion。
5. 把 scale 模型分级：`none -> global -> per-frame -> grid -> rejected`。只允许最小复杂度模型通过，不要一上来用 grid 抹平所有问题。
6. 检查 lifecycle anchor `mu_s` 到底来自 `depths` 还是 `depths_prior`。现在两个 scale ratio 不同，这很可能是 source mismatch。
7. 增加 “scale cannot write back” 字段：`diagnostic_scale_only=true`，避免后续误把 diagnostic scalar 当 geometry truth。
8. 加一个反例 gate：随机 anchors / shuffled frame 如果也能被单 scale 救回来，说明 gate 太弱。
9. 对正常/异常分类：global scale 稳定是 convention；per-frame 漂是 trajectory-depth gauge drift；tile-scale 明显是 JDSA 问题；normal/view 相关是 pose/intrinsics 或 depth projection convention。
10. 可以做 `Sparse-Dense-ScaleWitness`：DPVO sparse depth、HI-SLAM2 depth、anchor projected depth 三方只读比较，借鉴 SDAR，但不闭环反馈。

### B. Candidate vs CertifiedPacket

1. 现在不要 materialize `CertifiedGeometryPacket`。最多生成 `ScaleAlignedCandidatePacket` 或 `ScaleDiagnosedCandidatePacket`。
2. 如果将来允许 scale 进入 certification，它必须成为 version tuple：`pose_version + depth_version + normal_version + scale_grid_version + intrinsics_version`。
3. unscaled live projection 不通过时，`certification_candidate_ready` 应降级为 `candidate_ready_but_scale_unresolved`。
4. CertifiedPacket 的核心不是“有个 scale 后误差低”，而是“scale 来源、适用范围、版本、失败边界可审计”。
5. 严禁 silent multiply depth。这个会直接破坏 `Pose-Depth Co-Versioning` 的逻辑。

### C. Online-Lite 成熟度

1. center/depth live projection 是最低门槛，不够支撑 anchor maturity。
2. 第一个 GS 前最低应有：live depth agreement、normal agreement、versioned evidence cache、component-neighborhood duplicate check、free-space/surface-band check、dynamic-risk negative evidence。
3. photometric patch evidence 不一定阻塞 “schema-level dry run”，但应阻塞真正 anchor maturity / online birth。
4. cached replay 继续保留为 regression proof；live evidence 才能作为 runtime support。
5. evidence cache 要加 stale invalidation：pose/depth/normal/scale/intrinsics 任一变，相关 evidence 全部过期。

### D. DPVO 角色

1. 不重开 DPVO 作为 authority；可以重开为 **anchor-conditioned seeded patch tracker**。
2. DPVO 最有价值的是 `Σ / δ / BA residual / depth update magnitude / survival length`，不是 free-running track identity。
3. 用 DPVO 给 Anchor-PnP 提供 search center/radius，而不是让它决定 anchor 是否存在。
4. 用 DPVO 做 negative evidence：DPVO 稳、HI-SLAM2 depth scale 不稳，指向 depth convention；DPVO 也漂，指向 motion/texture/scene issue。
5. 用 DPVO 触发 online-lite top-up：support 下降时，只建议查哪里，不直接 birth。
6. 在 scale 问题解决前，不建议投入 DPVO segmented trajectory；它之前已经死在 overlap/support gate。

### E. Anchor-PnP

1. 暂停作为 pose certificate；可重命名为 `PatchSearchCandidateKernel`，降低心理负担。
2. 方向一：DPVO motion prior + 小窗口 ZNCC / NCC，不做全图 step=1。
3. 方向二：coarse-to-fine crop，高分辨率局部 refine，避免 coarse peak 锁错。
4. 方向三：multi-hypothesis PnP，不要单峰 winner-take-all；保留 top-k matches 进 RANSAC。
5. 方向四：LightGlue/ALIKED 只做 sparse pair verification，尤其用于 keyframe-pair / loop / relocalization。
6. 方向五：inverse-compositional LK on tangent patch，适合 mature anchor 小运动 refine。
7. 方向六：Sim3-PnP diagnostic，专门暴露 monocular scale 问题，不作为 SE3 pose source。
8. 必须保留 shuffled/random/affine controls，否则 photometric/PnP 很容易自欺。

### F. GS Timing

1. 当前不要跑真实 GS consumption。
2. 可以跑非消费型 `GSInterfaceDryRun`：只检查 packet schema、version propagation、seed count、coverage、memory budget，不创建可优化 GS map。
3. 真正 fixed GS smoke 至少等：scale issue 分类完成、CertifiedPacket materialization path reviewed、GS 只读消费边界测试通过。
4. 第一版 GS smoke 应 geometry frozen，只允许 color/opacity/appearance residual，不允许 pose/depth/anchor/scale 更新。
5. GS 指标不要只看 PSNR；必须同时看 rendered depth vs certified depth、free-space violation、Gaussian overgrowth、source packet provenance。

### G. AnchorGraph / ChildGS 梯度路线

1. 先做 read-only `AnchorComponentTable`，不是 AnchorGraph commit。
2. 下一步做 `CertifiedAnchorIndex`：component id、support frames、scale stability、normal stability、duplicate links、dynamic risk。
3. 再做 `owner_anchor_id` metadata-only GS fixture，不允许 ownership 影响几何。
4. 最后才是 anchor-owned residual GS。ChildGS 是 appearance/detail layer，不是 geometry layer。
5. 多尺度 admission：coarse plane/surface 先成熟，fine detail 后成熟；不要在粗结构没稳定前 birth fine Gaussians。
6. 加 quarantine 状态：loop correction 或 scale version 改变后，旧 anchors 不删除，先降级重审。

### 可能马上会遇到的新问题

1. intrinsics 与 resize/crop 不一致，会表现得像 scale 错。
2. normal 是 camera frame 还是 world frame，可能现在只检查 unit norm，没检查坐标约定。
3. keyframe id / timestamp / depth frame id 错位，会被 global scale 假装修好。
4. flat wall anchor 过集中仍会复发，需要 normal-bucket + spatial bucket 双约束。
5. dynamic foreground 会造成 depth pass 偶然失败，应加入 occlusion-vs-exposure 分类。
6. loop/global BA 后 packet version 会大面积失效，需要 provenance 和 rollback。
7. GS 很可能用 opacity/scale/color 吃掉几何错误，所以 GS smoke 不能作为 certification evidence。

### 建议的最近 10 个实验

1. per-frame scale table：`depths` vs `depths_prior`。
2. per-tile scale heatmap。
3. per-normal-bucket scale。
4. per-depth-bin scale。
5. anchor source audit：anchors 从哪个 depth source 来。
6. intrinsics resize/crop projection sanity。
7. scale 后 normal-depth consistency。
8. free-space/surface-band check before/after scale。
9. DPVO sparse depth vs HI-SLAM2 depth read-only comparison。
10. 20 个 anchors 的 live patch ZNCC prototype，只做 evidence cache，不做 admission。

