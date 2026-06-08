# SkelGS-SLAM Project Progress Record

Date: 2026-06-08

This record is a human-readable snapshot for local Obsidian notes. The active
source of truth in the repository remains `CURRENT_STATE.md`; archived material
is in `archive/`; experiment evidence is in `outputs/`; detailed change history
is in git commits.

## 0. One-Sentence Current State

The project has moved from trying to directly repair geometry into building a
read-only, certified frontend packet path:

```text
HI-SLAM2 / stronger frontend packet producer
  + AnchorLifecycleLite support audit
  -> read-only CertifiedGeometryPacket candidate
  -> later GS fixed consumption only after explicit admission
```

The strongest current result is:

```text
AnchorLifecycleLite cached online replay passes on room0 / office1 / office2,
with room0 needing bounded targeted top-up and office1/office2 passing from
dense_stride4 cached support directly.
```

Nothing is yet allowed to mutate VideoBuffer, produce a real CertifiedPacket,
feed GS, commit anchors, or use DPVO as authority.

## 1. Hard Invariants

```text
VideoBuffer remains the persistent frontend source of truth.
GS may consume certified geometry packets but must not silently overwrite
VideoBuffer pose, depth, intrinsics, gauge, normals, confidence, or geometry
weights.
Candidate geometry packets are evaluator/materialization artifacts unless
explicitly committed by a reviewed path.
No hidden pose / depth / gauge / scale / SE3 / Sim3 / render-loss optimization.
No hidden P-v5, render metrics, masks, confidence gates, or packet mutation as
evidence unless explicitly requested.
No GS residual writes pose/depth/anchors.
No anchor module directly mutates CertifiedPacket.
No DPVO track or pose is authority by itself.
```

Authority flags (all `= false`):

```text
certified_packet_materialized = false
gs_facing_packet_allowed = false
gs_consumption_allowed = false
video_buffer_writeback_allowed = false
anchor_mutation_allowed = false
dpvo_authority_allowed = false
```

## 2. Route Verdict Summary

### Kept As Current Main Direction

```text
AnchorLifecycleLite
  + HI-SLAM2 frontend / full trajectory / depth-normal packet producer
  -> read-only CertifiedGeometryPacket candidate
  -> later fixed GS consumption only after explicit admission
```

### Retained But Demoted

```text
DSO-style sparse direct kernel:
  retained as possible local refinement / diagnostic kernel
  rejected as direct-only pose certificate

DPVO:
  retained as untrusted local PoseCandidate / VO fallback / external baseline
  frozen as anchor accelerator and pre-certification source

HI-SLAM2:
  useful as bootstrap / fallback / baseline / practical frontend packet producer
  not a permanent unquestioned high-frequency frontend authority

MASt3R:
  appendix / oracle / audit baseline only
  not mainline geometry source
```

### Frozen / Rejected As Main Route

```text
UGG active anchor admission
old ACG posterior mutation
AnchorGraph online birth / commit
ChildGS
PDC-S0 / G1/J0 pairwise WCE optimizer as admitted candidate conditioner
PDC-O2 optimizer from G1 family
confidence residual weighting R3
semi-dense image evidence as construction source
free-running DPVO anchor-generation accelerator
DPVO segmented trajectory pre-certification
direct-only Anchor-DSO pose certificate
```

## 3. Anchor-DSO Branch

Attempted: `Depth/Normal Prior → FrozenMatureAnchorFixture → photometric residual preflight → pose-only Anchor-DSO → CertifiedGeometryPacket → GS`

Result:

```text
outputs/anchor_dso_v0/synthetic_corner_v0/pyramid_rescue_attempt/

preflight = pass
hard_gate_pass = false
num_cases = 45
num_pass = 30
pass_rate = 0.6818
mean_rot_improvement = 0.8117
mean_trans_improvement = -0.7640
```

Interpretation: Fixed mature anchors produce useful photometric residual landscape. Pyramid Anchor-DSO improves rotation/residuals. Translation recovery unreliable. Residual reduction is not a pose certificate.

→ Direct-only Anchor-DSO pose certificate **rejected**.

## 4. DPVO Work

### DPVO PoseCandidate Path
Implemented: `src/frontend/dpvo_pose_candidate.py`, export/validation/alignment scripts.

Short-window room0:
- candidates = 12, accepted = 7, rejected = 5 (insufficient tracks)
- After first-frame GT alignment: mean rot error 0.0217 deg, trans 0.0499 m
- Non-GT HI-SLAM2 bootstrap: rot delta 0.0177 deg, trans 0.065 m
- Half-res len40: 40/40 trajectory, 26/40 track support pass

### DPVO Segmented Pre-Certification → **Frozen**
Segmented trajectory pre-certification for CertifiedGeometryPacket:

- W20/S10: 2/3 segment pass, 0/2 overlap pass
- W16/S8: 2/4 segment pass, 0/3 overlap pass

GPT Pro freeze verdict: DPVO pre-certification **frozen**.

"Frozen" role:
- Local untrusted PoseCandidate / VO proposal
- External VO baseline
- Debug signal for motion/drift/support diagnostics

"Forbidden":
- CertifiedGeometryPacket source
- VideoBuffer writeback source
- GS source
- Anchor mutation source
- Anchor identity/lifecycle authority
- Certified segment stitching trajectory

## 5. HI-SLAM2 Frontend / Packet Candidate

Implemented: `src/frontend/hislam2_certified_packet.py`, builder script, tests.

### Early Blocked Attempts
- Full trajectory without depth/normal arrays → blocked (depth_prior_missing, normal_prior_missing)
- Short depth/normal → blocked (frame coverage)
- Frontend-only len40 identity pose → blocked (trajectory_has_no_motion)

### Success: Warmup-Corrected HI-SLAM2 Frontend Packet (Room0 Len120)
- 11 keyframes exported
- depth_prior, depths, normals: all finite
- camera translation span 0.84 m, rotation span 30.5 deg
- Status: `certification_candidate_ready`, blocked_reasons = []

## 6. AnchorGenerationAudit

Synthetic controls pass:
- normal: 429/454 visual pass, 344 trusted, hard_gate_pass = true
- single_wall / low_texture / shuffled / random → all false (correct negatives)

Replica GT-pose/depth read-only audits (all pass):
- room0: 644 candidates, 451 trusted, visible_trusted_per_frame_min = 339
- office1: 611 candidates, 505 trusted, visible_trusted_per_frame_min = 479
- office2: 744 candidates, 616 trusted, visible_trusted_per_frame_min = 520

## 7. AnchorLifecycleAudit

Core definition: same-anchor = same small surfel support (not plane/point/texture/DPVO track).

Synthetic long-fixture: soft gate pass, strict precision 1.0, recall 0.33.

Replica 8-frame diagnostics:
- room0/office1/office2 all: hard_gate_pass = true, control_pass = true

## 8. OnlineAnchorLifecycleLite

Motivation: Full lifecycle proof too expensive (room0 120 frames: ~104 sec, main cost 99 sec fixture build).

### Restricted Sweeps on HI-SLAM2 Frontend Room0 Len120
- Keyframes only (11 frames): failed (unique components, coverage, normal ratio)
- stride4 + keyframes (37 frames): failed (unique_components)
- stride2 + keyframes (64 frames): **passed**, 156 unique, visible 130, coverage 0.58

### Online-Lite Replay
Implemented: `src/anchor_audit/online_lite_state.py`, cache, topup, replay scripts/tests.

Room0 stride4 replay:
- Base unique = 95
- After bounded topup (11 refs, 57 candidates) → unique = 152
- proof_equivalent_pass = true (with bounded ref overflow policy)

Office1: unique = 297, topup = 0, proof_equivalent_pass = true
Office2: unique = 299, topup = 0, proof_equivalent_pass = true

State-machine replay: 48 state updates, 48 triggers → suppressed to 12 (pending-topup suppression)

Evidence-cache state object: `OnlineLiteEvidenceCache` → future insertion point for true projection/patch evidence.

## 9. PDC / Hodge / Packet-Conditioner Status

| Module | Status |
|---|---|
| PDC-GP-O2 | Read-only / diagnostic unless reopened |
| PDC-S0 G1/J0 | Rejected as candidate conditioner |
| PDC-O2 optimizer | Rejected |
| Confidence residual R3 | Rejected (GS degradation) |
| UGG active anchor admission | Rejected (read-only sidecar only) |

## 10. GPU / Environment Note

- RTX 4090 available
- Default sandbox PyTorch: cuda_available=False
- Escalated py310 execution: cuda_available=True, device_count=1
- DPVO len40 full-res: CUDA OOM; len19 OK; half-res len40 OK

## 11. Current Best Claim

```text
A read-only OnlineAnchorLifecycleLite policy simulation can preserve runtime
coverage/observability and reach proof-equivalent unique support from cached
lifecycle evidence, while keeping GS, VideoBuffer, CertifiedPacket
materialization, DPVO authority, and anchor mutation disabled.
```

| Scene | Base unique | After topup | Proof eq? |
|---|---|---|---|
| room0 | 95 (stride4) | 152 (+11 refs, +57 candidates) | ✅ |
| office1 | 297 (stride4) | 297 | ✅ |
| office2 | 299 (stride4) | 299 | ✅ |

## 12. What Is Not Proven Yet

- True online projection/evidence cache
- True online candidate generation from live frame data
- Component-neighborhood association in runtime code
- Real CertifiedGeometryPacket materialization
- GS fixed consumption from certified packet
- Multi-scene HI-SLAM2 frontend len120 packet parity
- Online anchor birth / AnchorGraph commit / ChildGS ownership
- DPVO anchor-conditioned seeded tracking / motion-prior

## 13. Recommended Next Steps

1. Replace cached replay evidence with real projection/patch evidence updates feeding `OnlineLiteEvidenceCache`
2. Add projection/patch evidence cache keyed by anchor instance and frame
3. Add component-neighborhood association instead of all-pair for online checks
4. Connect online-lite support summary to HI-SLAM2 packet candidate gate as read-only support summary
5. Only after above, consider a fixed GS consumption smoke from a reviewed CertifiedGeometryPacket candidate

Still do not:
- Materialize CertifiedPacket without explicit review
- Feed GS directly from candidate or DPVO
- Write VideoBuffer from GS/DPVO/anchors
- Commit anchors online
- Reopen DPVO accelerator by free-running tracks
- Revive UGG / ACG / ChildGS
- Lower the 150 unique proof-equivalent threshold to make a result pass

## 14. Related Decision Log Entries

- [[decision-log|SkelGS-SLAM Decision Log]] — see entries from 2026-06-05 through 2026-06-08 for Anchor-DSO, DPVO freeze, HI-SLAM2 packet, OnlineAnchorLifecycleLite.
