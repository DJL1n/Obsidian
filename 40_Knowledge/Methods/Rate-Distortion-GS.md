# Rate-Distortion for GS Maps

## Goal

在 GS-SLAM 的 frozen submap compression 中，通过 rate-distortion trade-off 在存储体积和渲染质量之间取得平衡。ContextGS 的 training objective 提供了 reference。

## Background

ContextGS: L = rendering loss + entropy coding loss + masking/regularization。典型 rate-distortion 形式：rate（bitrate）与 distortion（PSNR/SSIM/LPIPS）联合优化。

## 对 SkelGS-SLAM 的借鉴

- Active map: distortion 优先（高精度 tracking/mapping）
- Mature submap: rate-distortion balanced
- Frozen/archived: rate 优先（最小存储，可接受轻微质量损失）

## Related notes

- [[ContextGS]]
- [[Anchor-Compression-for-SLAM]]
- [[Anchor-Level-Context-Model]]
