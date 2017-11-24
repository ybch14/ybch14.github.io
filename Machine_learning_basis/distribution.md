# Dirichlet distribution & Multinomial distribution

## Dirichlet distribution

狄利克雷分布（记作 $Dir(\mathbf{\alpha})$）是一种连续多变量的概率分布，参数为由正实数组成的向量 $\mathbf{\alpha}$。

- 参数：$\alpha_1, \dots, \alpha_K, K\ge 2, \alpha_i > 0$
- PDF：
$$f(\mathbf{X}; \mathbf{\alpha}) = \frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\Pi_{i=1}^K\Gamma(\alpha_i)}\Pi_{i=1}^K x_i^{\alpha_i-1}$$
- 均值：$E[X_i] = \frac{\alpha_i}{\sum_k \alpha_k}$
- 方差：$Var[X_i] = \frac{\alpha_i(\alpha_0 - \alpha_i)}{\alpha_0^2(\alpha_0+1)}, \alpha_0 = \sum_k\alpha_k$
- 协方差：$Cov[X_i, X_j] = \frac{-\alpha_i\alpha_j}{\alpha_0^2(\alpha_0+1)} (i\ne j)$