# Progressive Growing of GANs for Quality, Resolution, and Diversity (StyleGAN)

> arXiv:1812.04948 | 2018

## 核心贡献
提出渐进式训练和基于风格的生成器架构，实现高质量人脸图像生成。

## 方法
StyleGAN（具体为StyleGAN1）引入两个关键创新：渐进式生长（Progressive Growing），从4×4低分辨率开始训练，逐步增加分辨率到1024×1024，每个阶段添加新层并冻结低分辨率层；基于风格的架构（Style-Based Generator），将潜在向量z通过映射网络转换为中间表示W，再通过多个风格注入（Style Mixing）层注入到各层卷积操作中，实现精细的多尺度控制。风格注入使用AdaIN（Adaptive Instance Normalization），将风格特征注入到特征图的统计量中。论文在FFHQ数据集上训练，生成极其逼真的人脸图像，FID分数远超ProGAN。StyleGAN2进一步优化了重参数化，消除重参数化假象（reparameterization artifacts），提升图像质量和多样性。

## 影响
StyleGAN是高质量图像生成的里程碑，FID分数长期保持SOTA。直接催生了面部生成、肖像编辑、GAN艺术等应用。NeurIPS 2019 Outstanding Paper，被引超1万次，是GAN领域最具影响力的工作之一。

## 相关笔记
- [[GAN]]
- [[Alias-Free-GAN]]
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]

## 所属分类

[[Categories/Generative-Models]]
