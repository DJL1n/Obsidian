# VGGT Architecture

## Goal

Multi-view feed-forward geometry model: input N images → output camera + depth + point map + track。

## Components

- DINOv2 patchifier
- Alternating-Attention Transformer (24 blocks): frame-wise self-attn + global self-attn
- Camera head: rotation quaternion + translation + FOV
- DPT head: depth + pointmap + tracking feature + confidence
- Tracking module: CoTracker2 (using VGGT backbone features)

## Related notes

- [[VGGT]]
- [[Multi-View-Geometry-Packet]]
- [[Feed-Forward-vs-Optimization]]
