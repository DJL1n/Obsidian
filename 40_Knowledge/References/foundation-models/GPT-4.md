# GPT-4 Technical Report

> arXiv:2303.08774 | 2023

## 核心贡献
报告GPT-4大规模多模态语言模型的架构、训练方法和能力评估。

## 方法
GPT-4是一个多模态大规模语言模型，接受文本和图像输入，生成文本输出。模型采用混合专家（Mixture-of-Experts, MoE）架构，结合稠密Transformer层。训练包含多阶段过程：先在大规模文本和代码语料库上进行预训练，后经过人类反馈强化学习（RLHF）进行对齐优化，提升有用性、安全性和鲁棒性。模型还采用了宪法AI（Constitutional AI）方法，通过基于规则的系统反馈进行自我批评和对齐。论文在32个基准测试上评估了GPT-4，涵盖STEM、人文、编程、多模态理解等领域，在多项专业级考试（如律师资格考试、美国医学入学考试模拟）中达到前10%水平。模型展现出少样本推理、系统提示敏感性和复杂指令遵循能力，相比GPT-3.5在安全方面有显著提升。

## 影响
GPT-4是首个达到AGI雏形的语言模型，极大推动了AI对齐和多模态大模型研究。论文被Nature和Science引用，引发全球对AI安全和能力的广泛讨论。

## 相关笔记
- [[Attention]]
- [[InstructGPT-Training-Language-Models-to-Follow-Instructions]]
- [[RLHF]]
