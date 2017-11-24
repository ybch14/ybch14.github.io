# LDA

Latent Dirichlet Allocation，自然语言处理中一种非常重要的主题模型算法，通过在语料库上训练，期望得到两个结果向量：每个文档取不同主题的概率；每个主题取不同词语的概率。

## 两个分布

在分析 LDA 模型的时候，需要用到两个特殊分布的性质：

### Dirichlet distribution

狄利克雷分布（记作 $Dir(\mathbf{\alpha})$）是一种连续多变量的概率分布，参数为由正实数组成的向量 $\mathbf{\alpha}$。

- 参数：$\alpha_1, \dots, \alpha_K, K\ge 2, \alpha_i > 0$
- PDF：
$$f(\mathbf{X}; \mathbf{\alpha}) = \frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\Pi_{i=1}^K\Gamma(\alpha_i)}\Pi_{i=1}^K x_i^{\alpha_i-1}$$
- 均值：$E[X_i] = \frac{\alpha_i}{\sum_k \alpha_k}$
- 方差：$Var[X_i] = \frac{\alpha_i(\alpha_0 - \alpha_i)}{\alpha_0^2(\alpha_0+1)}, \alpha_0 = \sum_k\alpha_k$
- 协方差：$Cov[X_i, X_j] = \frac{-\alpha_i\alpha_j}{\alpha_0^2(\alpha_0+1)} (i\ne j)$

### Categorical distribution

类别分布，描述的是一个随机矢量的每一个元素都只能取有限个状态中的一个的情况下的概率分布。

- 参数：$k>0$ 个类别；每个类别的概率 $p_i, i = 1, 2, \dots, k$
- PMF：f(\mathbf{x}\|\mathbf{p}) = \Pi_{i=1}^kp_i^{[\mathbf{x}=i]}

