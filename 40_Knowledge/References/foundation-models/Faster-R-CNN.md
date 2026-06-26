# Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks

> arXiv:1506.01497 | 2015

## 核心贡献
提出Faster R-CNN，用区域提议网络替代传统候选区域生成，实现端到端目标检测。

## 方法
Faster R-CNN的核心创新是区域提议网络（Region Proposal Network, RPN），它取代了Faster R-CNN之前的选择性搜索（Selective Search）等外部候选区域生成方法。RPN是一个全卷积网络，在特征图上滑动窗口，对每个锚点（anchor）同时预测物体/背景二分类和边界框回归。RPN与检测网络共享卷积特征，实现高效推理。整个流程包括：输入图像通过骨干网络（如ResNet/VGG）提取特征；RPN在特征图上生成区域提议；RoI Pooling将不同大小的区域映射为固定尺寸特征；最后通过分类头和回归头输出类别和边界框。论文在MS COCO和PASCAL VOC上大幅超越当时SOTA，达到近实时检测速度（FPS > 15）。

## 影响
Faster R-CNN将目标检测从两阶段分离方法统一为端到端框架，是两阶段检测器的里程碑。被引用超过8万次，是目标检测领域的基础性工作，其锚点和RoI设计被后续模型广泛沿用。

## 相关笔记
- [[ResNet]]
- [[YOLO]]
- [[Mask-R-CNN]]

## 所属分类

[[Categories/Vision-Transformers]]
