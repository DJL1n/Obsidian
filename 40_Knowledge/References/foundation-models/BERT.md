# BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding

> arXiv:1810.04805 | 2018

## 核心贡献
提出双向预训练语言模型BERT，通过掩码语言模型实现深层上下文表示。

## 方法
BERT采用Transformer编码器结构，通过大规模无监督预训练+下游任务微调的范式。核心创新包括：掩码语言模型（Masked Language Model, MLM），随机遮蔽15%的token并预测其原始词元，实现双向上下文编码；下一句预测（Next Sentence Prediction, NSP）任务，学习句子级关系。预训练在BooksCorpus和Wikipedia共34亿词元上训练。BERT-Base为12层/768维度/12头注意力，BERT-Large为24层/1024维度/16头。预训练后在11个NLP任务（GLUE、SQuAD、RACE等）上进行微调，创造11项SOTA记录。掩码机制避免了自回归模型无法同时利用前后文的局限。

## 影响
开创了大规模预训练+微调的NLP范式，直接启发了GPT、RoBERTa、ALBERT、DistilBERT等后续工作。BERT是NLP领域引用量最高的论文之一，推动Transformer在NLP中的统治地位。

## 相关笔记
- [[Attention]]
- [[GPT-4]]
- [[UNITER]]
