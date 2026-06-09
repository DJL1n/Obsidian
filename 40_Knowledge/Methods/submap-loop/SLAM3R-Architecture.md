# SLAM3R Architecture

## Goal

RGB-only real-time dense reconstruction via sliding-window local pointmap reconstruction + learned local-to-world registration + retrieval-guided historical reference selection。

## Components

### I2P
Sliding window → shared encoder + keyframe decoder (multi-view cross-attention) + supporting decoder + point regression head → local pointmaps + confidence。

### Scene initialization
First window → try each frame as keyframe → highest confidence → world coordinate。

### Reservoir + retrieval
Bounded reservoir sampling → retrieval: top-k correlated scene frames (visual + baseline suitability)。

### L2W
Joint appearance+geometry tokens → registration decoder (local→global) + scene decoder (refine, no coordinate change)。

### No explicit camera parameters / BA / pose graph。

## Related notes

- [[SLAM3R]]
- [[I2P-Local-Reconstruction]]
- [[L2W-Global-Registration]]
