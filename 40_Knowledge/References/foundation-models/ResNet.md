# Deep Residual Learning for Image Recognition (ResNet)

> arXiv:1512.03385 | 2015

## 核心贡献
提出残差连接解决深层网络退化问题，使训练极深网络成为可能。

## 方法
ResNet提出残差学习框架，解决深度网络训练中的退化问题：网络越深，准确率反而下降（不是过拟合，而是优化困难）。核心创新是残差连接（Residual Connection/H skip connection），让网络学习残差映射H(x) - x而非直接映射H(x)，即用F(x) + x替代F(x)，其中F(x)为残差函数。残差连接允许梯度直接反向传播到浅层，缓解了梯度消失问题。论文设计了基础残差块（两层3×3卷积）和瓶颈残差块（3×3×3结构），在ImageNet上训练了152层网络（ResNet-152），远超VGG-19。ResNet-152的top-5错误率为6.7%，参数量却少于GoogLeNet。论文进一步展示了1000+层的残差网络，误差仅为3.59%，远超人眼水平（5.1%）。

## 影响
ResNet是深度学习史上最被引用的论文之一（被引超过20万次），获CVPR 2016最佳论文。残差连接已成为几乎所有深度网络的标配组件，从GPT到Diffusion Model到ViT均采用类似设计。

## 相关笔记
- [[Attention]]
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]
- [[Vision-Transformers]]

## 所属分类

[[Categories/Vision-Transformers]]
