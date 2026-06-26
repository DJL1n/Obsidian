# Generative Adversarial Nets (GAN)

> arXiv:1406.2661 | 2014

## 核心贡献
提出生成对抗网络框架，通过对抗训练学习数据分布。

## 方法
GAN由生成器（Generator）和判别器（Discriminator）两个神经网络组成，二者通过 minimax 博弈联合训练。生成器G接收随机噪声z，生成假样本G(z)；判别器D接收真实样本或生成样本，输出其为真实的概率。训练目标是极小极大值：min_G max_D V(D,G)，其中V包含两个部分——D最大化识别真实样本和生成样本的能力，G最小化被D正确识别的能力。理论上当G和D都达到最优时，G学习到真实数据分布pdata。论文使用深度CNN作为网络架构，在MNIST和ImageNet等数据集上展示了生成能力。判别器本质上是一个二分类器，生成器试图"欺骗"判别器。GAN的训练过程是一个动态博弈，两个网络相互促进、交替提升。

## 影响
GAN彻底改变了生成模型领域，催生了DCGAN、WGAN、StyleGAN、CycleGAN等数百种变体。ICML 2014论文，被引超过10万次，是深度学习领域引用最高的论文之一。GAN是生成AI的三大支柱之一（VAE、GAN、Diffusion）。

## 相关笔记
- [[StyleGAN]]
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]
- [[Alias-Free-GAN]]
