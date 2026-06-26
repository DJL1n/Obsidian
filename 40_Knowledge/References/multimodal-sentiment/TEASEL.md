# TEASEL: Transformer-Based Speech-Prefixed Language Model

> arXiv:2211.03421 | 2022

## 核心贡献
提出 speech-prefixed language model 架构，用语音特征作为 prefix tokens 注入语言模型，实现更自然的语音-文本跨模态理解。

## 方法
将 Wav2Vec2 提取的语音特征序列通过线性投影转化为 prefix tokens，放在 text tokens 之前输入 Transformer。语言模型在解码时自然 attend 到语音 prefix，无需额外融合模块。相比 late fusion 和 cross-attention 方案，在对话情感分析任务上取得 SOTA，计算开销更低。
