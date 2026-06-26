# Attention Is All You Need

> arXiv:1706.03762 | 2017

## 核心贡献
提出Transformer架构，完全基于注意力机制，抛弃循环和卷积。

## 方法
论文提出Transformer，一种完全基于自注意力机制（Self-Attention）的序列转序列模型。核心组件包括多头注意力（Multi-Head Attention）、位置编码（Positional Encoding）、点状前馈网络（Position-wise FFN）和残差连接与层归一化。编码器由6层相同的堆叠层组成，每层包含多头自注意力和FFN子层；解码器同样包含自注意力和编码器-解码器注意力。与传统RNN/CNN相比，自注意力允许任意位置的token直接交互，实现了更好的并行训练和更长的依赖捕捉。模型在WMT 2014英德翻译任务上达到28.4 BLEU，在英法翻译上达到41.8 BLEU，均打破当时SOTA。

## 影响
Transformer已成为NLP和CV的骨干架构，为BERT、GPT、T5、ViT等奠定了基础。是2017年NIPS最佳论文，被引超过10万次。后续几乎所有大语言模型和视觉Transformer均基于此架构。

## 相关笔记
- [[BERT]]
- [[GPT-4]]
- [[Vision-Transformers]]
