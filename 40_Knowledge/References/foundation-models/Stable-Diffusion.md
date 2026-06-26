# High-Resolution Image Synthesis with Latent Diffusion Models (Stable Diffusion)

> arXiv:2112.10752 | 2021

## 核心贡献
在潜在空间进行扩散，将计算成本降低40倍，实现高效高分辨率图像生成。

## 方法
Latent Diffusion Models (LDM)将扩散过程从像素空间转移到低维潜在空间，大幅降低计算成本。框架由三部分组成：冻结的变分自编码器（VAE）将图像编码为紧凑潜在表示；扩散模型在潜在空间执行加噪-去噪过程；条件化机制（Classifier-Free Guidance, CFG）支持文本、类别、深度图等条件引导。核心设计：在潜在空间而非像素空间进行扩散，将计算量降低约40倍；使用预训练的CLIP文本编码器作为条件输入，实现文本到图像生成；采用U-Net架构作为去噪网络，注入跨注意力和时间嵌入。在FID分数上，LDM在多个生成任务（无条件、条件、图像编辑、超分辨率）上超越GAN和纯像素扩散模型。Stable Diffusion（基于LDM）是第一个开源的高质量文生图模型。

## 影响
LDM/Stable Diffusion是AIGC革命的核心技术，直接催生了Midjourney、DALL·E 2、SDXL等商业产品。论文获NeurIPS 2022杰出论文，开源版本极大推动了社区创作和研究。

## 相关笔记
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]
- [[CLIP]]
