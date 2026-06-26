# UNITER: Universal Image-Text Representation Learning

> arXiv:2003.01206 | 2020

## 核心贡献
提出统一的图像-文本预训练模型，将视觉和语言token平等对待。

## 方法
UNITER将图像区域特征和文本token视为平等对待的统一序列输入。核心设计：图像被Region Proposal Network（RPN）分割为固定数量的区域，每个区域的特征（RoI特征）与文本token一起输入Transformer编码器。预训练任务包括：图像-文本匹配（ITM）和文本到图像生成（TIG）。TIG任务将图像token用掩码替换，让模型通过文本和可见区域预测被掩码的区域，实现视觉特征的自监督学习。UNITER在MS COCO的视觉问答（VQA）、图像-文本检索（ITR）、自然语言推理（NLVR）等任务上取得SOTA。模型统一了视觉和语言token的处理方式，不区分模态，所有token通过相同的Transformer层进行交互，实现真正的全模态融合。

## 影响
UNITER是多模态预训练的开创性工作之一，启发了VL-BERT、VL-T5、ALIGN等后续模型。证明了统一处理多模态token的有效性，为CLIP等大规模视觉-语言模型奠定了思路基础。

## 相关笔记
- [[CLIP]]
- [[BERT]]
- [[Visual-Question-Answering]]

## 所属分类

[[Categories/Vision-Language]]
