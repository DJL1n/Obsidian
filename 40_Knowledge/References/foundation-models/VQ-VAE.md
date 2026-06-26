# Neural Discrete Representation Learning (VQ-VAE)

> arXiv:1711.00937 | 2017

## 核心贡献
提出向量量化VAE，将连续潜在空间离散化为可学习的码本，实现可微的离散表示学习。

## 方法
VQ-VAE将标准VAE的连续潜在变量替换为离散码本中的向量。编码器输出一个连续特征向量，通过最近邻查找映射到码本（codebook）中的离散嵌入向量，解码器基于该离散向量重构输入。关键创新在于使用直方编码（straight-through estimator）技巧：前向传播时直接替换为码本向量，反向传播时梯度绕过量化操作直接传回编码器输出。码本向量作为可学习参数通过在线k-means更新。论文进一步展示了VQ-VAE在生成模型中的应用——将离散潜在变量作为自回归模型（如PixelCNN++）的输入，在ImageNet上生成高质量图像。VQ-VAE-2引入两阶段方法，先学习低分辨率潜在空间，再学习残差，实现了ImageNet上的FID大幅改进。

## 影响
VQ-VAE成为离散表示学习的基石，被VQGAN、DALL·E、Taming Transformers、Make-A-Video等广泛采用。NeurIPS 2017最佳论文提名，开启了基于离散token的视觉-语言生成范式。

## 相关笔记
- [[VAE]]
- [[beta-VAE]]
- [[DALL-E]]

## 所属分类

[[Categories/Generative-Models]]
