# RTG-SLAM Architecture

## Goal

RGB-D 大场景实时 Gaussian SLAM: compact opaque/transparent GS + explicit birth + stable/unstable state + ICP tracking + ORB backend。

## Components

- Compact GS: opaque (surface + main color) + transparent (residual color)
- Depth rendering: ray-plane intersection with first opaque hit (≠ alpha blending)
- Explicit birth: new area / depth error / color error
- State mgmt: stable / unstable / outlier
- Tracking: frame-to-model ICP (depth/normal)
- Backend: ORB-SLAM2-style graph opt, sparse keyframe, top color-error pixels only

## Related notes

- [[RTG-SLAM]]
- [[Primitive-Lifecycle-State]]
- [[Explicit-Birth-Event]]
