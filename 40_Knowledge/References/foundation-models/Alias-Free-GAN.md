# Alias-Free Generative Adversarial Networks

> arXiv:2312.06236 | 2023

## 核心贡献
提出无混叠卷积和激活操作，消除GAN中的频率混叠伪影。

## 方法
Alias-Free GAN分析并解决了生成模型中的混叠（aliasing）问题。在标准GAN的生成器中，上采样→卷积→激活的链式操作会在高频处产生混叠伪影。论文提出两个关键组件：无混叠卷积（Alias-Free Convolution），在上采样后先用低通滤波（sinc核）去除高频分量，再进行卷积和激活；无混叠激活（Alias-Free Activation），将非线性激活（如ReLU/LReLU）放在频域中处理，避免激活函数在高频处产生混叠。同时提出基于傅里叶分析的方法来量化和可视化这些混叠伪影。使用无混叠组件后，StyleGAN2和BigGAN的FID分数显著改善，生成的图像质量更高、更真实。论文还分析了判别器端的混叠问题，并提出相应的无混叠判别器架构。

## 影响
该工作揭示了GAN生成质量中混叠伪影这一长期被忽视的问题，NeurIPS 2024论文。为GAN架构改进提供了新的理论视角，推动了频域分析在生成模型中的应用。

## 相关笔记
- [[GAN]]
- [[StyleGAN]]
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]

## 所属分类

[[Categories/Generative-Models]]
