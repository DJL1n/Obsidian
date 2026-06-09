# L2W Global Registration

## Definition

通过 learned feed-forward network 将局部窗口的 3D pointmap 注册到全局 scene coordinate system，无需传统 iterative ICP / PnP / BA。SLAM3R 的 L2W 是代表实现。

## Why it matters

传统多视图 pointmap 对齐需要 expensive global alignment optimisation（DUSt3R）。L2W 用 network 学习式注册，大幅提速。且 L2W 不单独依赖 appearance，而是 joint appearance + geometry token，使注册更鲁棒。

## Related notes

- [[SLAM3R]]
- [[I2P-Local-Reconstruction]]
- [[Retrieval-Guided-Registration]]
