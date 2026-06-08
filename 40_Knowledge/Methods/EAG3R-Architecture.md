# EAG3R Architecture

## Goal

Event-augmented MonST3R: Retinex + SNR + event adapter + cross-attention + event photometric consistency loss for low-light/dynamic pointmap geometry。

## Components

- LightUp: Retinex-inspired → enhanced RGB + SNR map
- Event adapter: Swin Transformer, pretrained, lightweight
- Fusion: event query cross-attention + SNR-aware concat
- Backbone: MonST3R decoder + DPT heads (frozen encoder)
- Global opt: alignment + flow + smoothness + event photometric consistency

## Related notes

- [[EAG3R]]
- [[SNR-Aware-Reliability-Gating]]
- [[Motion-Geometry-Coherence-Loss]]
