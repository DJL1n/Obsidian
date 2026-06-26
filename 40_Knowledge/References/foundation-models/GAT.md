# Graph Attention Networks (GAT)

> arXiv:1710.10903 | 2017

## 核心贡献
提出图注意力网络，用自注意力机制学习图节点表示，替代图卷积的归一化方案。

## 方法
GAT引入注意力机制到图神经网络中，替代GCN中基于图结构的固定归一化方案。对于每个节点i及其邻居v_j，GAT计算注意力系数e_ij = a(h_i, h_j)，其中a是可学习的注意力函数，h为节点特征。注意力系数通过Softmax归一化，加权聚合邻居信息。核心创新包括：多头注意力（Multi-Head Attention），对每个头独立计算注意力并拼接/平均，提升模型稳定性和表达能力；可学习注意力权重使模型能根据不同邻居的重要性自适应分配权重，无需预知图结构。GAT采用自注意力形式，不依赖图的拉普拉斯矩阵或邻接矩阵归一化。论文在Cora、CiteSeer、PubMed等图分类数据集上超越GCN，在PPI蛋白相互作用数据集上也取得SOTA。

## 影响
GAT是图注意力网络的开创性工作，被引超过3万次，推动了图神经网络从谱方法向注意力方法的转变。是Graph Transformer、GATv2、GraphMAE等后续工作的直接基础。

## 相关笔记
- [[Graph-Neural-Networks]]
- [[Attention]]
- [[Graph-Transformers]]
