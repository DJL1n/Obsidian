# Multi-Modal Uncertainty

## Definition

在 dynamic GS-SLAM 中，同时使用 color residual、depth residual、DINO feature residual 和 transmittance/visibility mask 作为多模态证据，联合优化或训练 uncertainty / dynamic mask。

## Why it matters

单靠 RGB residual 受曝光/纹理影响大。Depth residual 提供几何不一致。DINO feature residual 提供更鲁棒的语义/结构表征。Transmittance 防止未建图区域误判为动态。多模态联合比单一信号更可靠。

## Related notes

- [[gs-slam/dynamic/UP-SLAM]]
- [[Probabilistic-Anchor-Update]]
- [[Certified-Probabilistic-Anchor]]
