# NER with LM

截至 2018 年 NER 领域的 state-of-the-art。原文链接：[https://arxiv.org/pdf/1705.00108.pdf](https://arxiv.org/pdf/1705.00108.pdf)。

主要贡献：

- 证明了序列标注任务中语言模型嵌入向量的作用。加入了预训练的 LM 嵌入后，NER 性能从 90.87% 上升至 91.93%。
- 证明了双向 LM 的性能强于一个单向的 LM。

## TagLM

TagLM 是指用语言模型增强的序列标注器。首先用无标注数据训练词嵌入和语言模型，得到每个词的词嵌入和 LM 嵌入向量。这两个向量可以用于增强序列标注模型的性能。

### Baseline 序列标注模型

每个词由字符级特征和词语级特征拼接得到。字符级特征由 CNN 或者 RNN 提取。词语级特征一般由查找预训练的 embedding 矩阵得到，在训练过程中微调。为了学习语义敏感的特征向量，需要用到多层的双向 RNN。双向 RNN 的好处是能够同时使用过去的和未来的信息。这项工作中使用两层 RNN。最后，RNN 的隐状态用于预测每个词对应的标注。为了保证连续的词标注之间的依赖关系，此处加入 CRF 层，用前向-后向算法进行训练，用 Viterbi 算法进行预测。

### 双向语言模型

语言模型计算一列词语出现的概率：$p(t_1, t_2, \dots, t_n) = \prod_{k=1}^N p(t_k\|t_1, \dots, t_{k-1}).$ 目前的语言模型用多层 LSTM 将 $(t_1, t_2, \dots, t_{k-1})$ 嵌入到一个低维向量空间中。此为*前向语言模型*。同理，为了获取未来的语境，可以再引入一个*反向语言模型*：$p(t_1, t_2, \dots, t_N) = \prod_{k=1}^N p(t_k\|t_{k+1}, \dots, t_N).$ 在最终的系统中，两个语言模型的 softmax 层都去掉，把每个词对应的隐状态向量连接起来得到 LM 嵌入向量。注：两个 LM 没有共享的参数，全部独立。LM 嵌入向量再连接到序列标注模型中第一层 LSTM 的隐状态向量上，得到最终的 TagLM 系统。

## 实验

- 数据集：CoNLL 2003 NER
- 字符编码：两层双向 GRU，隐状态维度 80，字符嵌入维度 25。
- 序列标注：两层双向 GRU，隐状态维度 300。每个 GRU 输入向量加 25% dropout 防止过拟合。
- 语言模型：单向 LSTM，隐状态维度 2048，LM 嵌入向量维度 512。
- 语言模型训练语料：1B World Benchmark
- 优化器：Adam，梯度截断
- NER 实验结果

|Model|F值|
|:-:|:-:|
|Chiu 2016|90.91|
|Lample 2016|90.94|
|Ma 2016|91.37|
|baseline|90.87|
|TagLM|91.93|

## 参考文献

- Peters, M. E., Ammar, W., Bhagavatula, C., & Power, R. (2017). Semi-supervised sequence tagging with bidirectional language models. In Proceedings of ACL. https://doi.org/10.18653/v1/P17-1161
