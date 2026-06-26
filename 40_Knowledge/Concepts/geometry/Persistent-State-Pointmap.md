# Persistent State Pointmap

## Definition

一种 recurrent 3D perception 范式：模型通过 persistent latent state 持续维护对场景的理解，每输入一张新图像就更新 state 并输出 metric-scale pointmap。CUT3R 是代表实现。

## Why it matters

Pairwise pointmap（DUSt3R）缺少 persistent scene state，视频累积易 drift。Persistent state 让模型可以像人一样边看边更新 3D 理解，输出 common-coordinate pointmaps，无需外部 alignment。

## Related notes

- [[geometry-model/CUT3R]]
- [[Certified-Geometry-Memory]]
- [[CUT3R-Architecture]]
