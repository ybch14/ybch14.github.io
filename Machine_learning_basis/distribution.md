# Dirichlet distribution & Multinomial distribution

## Dirichlet distribution

狄利克雷分布（记作 $Dir(\mathbf{\alpha})$）是一种连续多变量的概率分布，参数为由正实数组成的向量 $\mathbf{\alpha}$。

- 参数：$\alpha_1, \dots, \alpha_K, K\ge 2, \alpha_i > 0$
- PDF: $\frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\Pi_{i=1}^K\Gamma(\alpha_i)}\Pi_{i=1}^K x_i^{\alpha_i-1}$