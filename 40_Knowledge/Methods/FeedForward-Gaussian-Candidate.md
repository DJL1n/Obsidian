# FeedForward Gaussian Candidate

## Goal

将 Flash-Mono 的 Predict-and-Refine 范式转化为你的 admission gate：feed-forward model 产生 Gaussian candidate → admission test → CertifiedGeometryPacket / GS birth。

## Design

### FeedForwardGaussianCandidate
- Pose
- Point / depth
- Normal
- Surfel scale
- Opacity
- Color
- Confidence
- Hidden-state descriptor (optional)

### Admission test
- Depth-normal consistency
- Temporal repeatability
- Flow consistency (DPVO/DROID)
- Dynamic risk
- Multi-view support

### Decision
- Pass → CertifiedGeometryPacket / GS birth
- Fail → discard or weak candidate only

## Related notes

- [[Flash-Mono]]
- [[Predict-and-Refine-Gaussian]]
- [[Hidden-State-Submap-Descriptor]]
