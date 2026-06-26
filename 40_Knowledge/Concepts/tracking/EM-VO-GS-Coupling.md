# EM VO-GS Coupling

## Definition

在 monocular GS-SLAM 中，通过 Expectation-Maximization 框架交替优化 VO state（camera poses + depths）和 GS scene（Gaussian attributes）。E-step fix P,D → optimize G；M-step fix G → optimize P,D。GSO-SLAM 是代表实现。

## Why it matters

松耦合 VO→GS 浪费信息。完全耦合 GS tracking 计算高。EM 耦合让 VO semi-dense depth 约束 GS，GS rendered depth 反过来帮助 VO depth，且不引入额外计算成本（VO 已有的 gradients/associations 可复用）。

## Related notes

- [[slam-frontend/GSO-SLAM]]
- [[Frontend-Gaussian-Initialization]]
- [[Certified-EM-Coupling]]
