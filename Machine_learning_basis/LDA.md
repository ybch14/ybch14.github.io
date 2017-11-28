# LDA

Latent Dirichlet Allocation，自然语言处理中一种非常重要的主题模型算法。LDA的核心思想是利用语料库进行训练，期望对语料库中的每个文档选定一个主题，然后统计得到每个主题下的词语分布。有了每个主题下的词语分布，对于一个新的文档，就可以通过其中的词来估计文档的主题，实现对文档语义的理解。

## 两个分布

在分析 LDA 模型的时候，需要用到两个特殊分布的性质：Dirichlet 分布和 Categorical 分布。两种分布互为共轭分布。

### Dirichlet distribution

狄利克雷分布（记作 $Dir(\mathbf{\alpha})$）是一种连续多变量的概率分布，参数为由正实数组成的向量 $\mathbf{\alpha}$。

- 参数：$\alpha_1, \dots, \alpha_K, K\ge 2, \alpha_i > 0$
- PDF：
$$f(\mathbf{X}; \mathbf{\alpha}) = \frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\prod_{i=1}^K x_i^{\alpha_i-1}$$
- 均值：$E[X_i] = \frac{\alpha_i}{\sum_k \alpha_k}$
- 方差：$Var[X_i] = \frac{\alpha_i(\alpha_0 - \alpha_i)}{\alpha_0^2(\alpha_0+1)}, \alpha_0 = \sum_k\alpha_k$
- 协方差：$Cov[X_i, X_j] = \frac{-\alpha_i\alpha_j}{\alpha_0^2(\alpha_0+1)} (i\ne j)$

### Categorical distribution

类别分布，描述的是一个随机矢量的每一个元素都只能取有限个状态中的一个的情况下的概率分布，是多项分布的一种特殊情况。其物理模型是已知有限个状态的概率分布，进行一次伯努利实验，实验结果的概率分布即为类别分布。（如果每次进行 n 次伯努利实验，即为多项分布的物理模型）

- 参数：$k>0$ 个类别；每个类别的概率 $p_i, i = 1, 2, \dots, k$
- PMF：$f(\mathbf{x}\|\mathbf{p}) = \prod_{i=1}^kp_i^{[\mathbf{x}=i]}$，$[x=i]={1 if x = i else 0}$
- 举例：假设有一种不均匀的骰子，k 个面朝上的概率分别为 $p_1, \dots, p_k$；投掷骰子 n 次，得到的结果为多项分布；如果只投掷一次的话，得到的结果为类别分布。

在 LDA 模型中，认为每个文档的主题分布和每个主题的词语分布遵循 Dirichlet 分布，每个词语所属的主题编号以及在词典中的编号遵循 Categorical 分布。以上两个假设刚好符合 Dirichlet-Multinomial 复合分布的思想：观测变量的概率分布由 Dirichlet 分布生成，观测变量则由以上一步生成的概率分布为参数的 Multinomial 分布生成。

## 模型定义

在 LDA 模型中，认为一个语料库中包含若干个文档（document）。每个文档属于一个主题（toprodc）。在每个主题中，词典中出现的所有词语都服从一个概率分布，不同主题的词语分布不同（例如在宠物话题中“狗”出现的概率比“篮球”出现的概率大很多，但是在“运动”话题中情况刚好相反）。
基于上面的假设，LDA 模型认为自然文本中每个文档的主题分布的先验为 Dirichlet 分布，参数为 $\alpha$（记作 $Dir(\alpha)$）；每个主题的词语分布的先验为 Dirichlet 分布，参数为 $\beta$（记作 $Dir(\beta)$）；自然文本的生成过程如下：
- 从 $Dir(\alpha)$ 中生成每个文档的主题分布向量 $\theta_i$；
- 从 $Dir(\beta)$ 中生成每个主题的词语分布向量 $\phi_k$；
- 对于第 $i$ 个文档的第 $j$ 个位置：
    - 从 $Categorical(\theta_i)$ 中生成其主题 $z_{i,j}$
    - 从 $Categorical(\phi_{z_{i,j}})$ 中生成其词语 $\omega_{i, j}$

## 符号说明

|符号|类型|含义|
|:-:|:-:|:-|
|$K$|int|主题数目|
|$V$|int|词典长度|
|$M$|int|文档数目|
|$N_{d = 1\dots M}$|int|文档 $d$ 中词的个数|
|$\alpha$|$K$ 维向量|文档话题的先验权重（主题分布的 Dirichlet 先验的参数，通常每个分量都小于 1）|
|$\beta$|$V$ 维向量|话题词语的先验权重（词语分布的 Dirichlet 先验的参数，通常每个分量都小于 1）|
|$\phi_{k=1\dots K}$|$V$维向量|主题 $k$ 中的词语分布|
|$\phi$|$K\times V$矩阵|词语分布的矩阵形式|
|$\theta_{d=1\dots M}$|$K$维向量|文档 $d$ 中的主题分布|
|$\theta$|$M\times K$矩阵|主题分布的矩阵形式|
|$z_{m=1\dots M,n = 1\dots N_d}$|int,$[1, K]$|文档 $m$ 中第 $n$ 个词的主题编号|
|$\mathbf{Z}$|$M\times N$矩阵|主题编号矩阵形式|
|$\omega_{m=1\dots M,n = 1\dots N_d}$|int,$[1, V]$|文档 $m$ 中第 $n$ 个词的词语编号|
|$\mathbf{W}$|$M\times N$矩阵|词语编号矩阵形式|

## 估计隐变量

对于含有隐变量的模型，通常训练的目标都是希望得到隐变量的分布。LDA 也是如此。在 LDA 模型中，隐含的变量就是每个词语所属的主题。因此训练期望的目标就是找到在已知模型参数和其他所有词语及其主题时，某一个词语的主题概率分布。想得到的分布是一个条件分布，因此考虑用 Gibbs 采样方法估计分布。首先考虑模型的联合概率分布（为了简化分析，假设所有文档长度相同，为 $N$）：

$$P(\mathbf{W}, \mathbf{Z}, \theta, \phi;\alpha, \beta) = \prod_{i=1}^KP(\phi_i;\beta)\prod_{j=1}^MP(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)P(\omega_{j, t}|\phi_{z_{j, t}})$$

由于联合分布中存在 $\theta$ 和 $\phi$，在进行 Gibbs 采样的时候比较麻烦，因此采用折叠 Gibbs 采样（collapsed Gibbs sampling），用积分消除 $\theta$ 和 $\phi$：

$$P(\mathbf{W}, \mathbf{Z};\alpha, \beta) = \int_\theta\int_\phi P(\mathbf{W}, \mathbf{Z}, \theta, \phi;\alpha, \beta)d\phi d\theta$$

$$ = \int_\phi\prod_{i=1}^KP(\phi_i;\beta)\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}})d\phi\int_\theta\prod_{j=1}^MP(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta$$

先考虑和 $\theta$ 有关的部分：

$$\int_\theta\prod_{j=1}^MP(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta=\prod_{j=1}^M\int_{\theta_j} P(\theta_j;\alpha)\prod_{t=1}^N P(Z_{j, t}|\theta_j)d\theta_j$$

接下来只看求积符号内部的表达式。很显然这个表达式代表了模型中和主题相关的隐藏部分。将 Dirichlet 分布的表达式代入可得：

$$\int_{\theta_j}P(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta_j = \int_{\theta_j}\frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\prod_{i=1}^K\theta_{j, i}^{\alpha_i-1}\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta_j$$

接下来问题的关键是求解 $\prod_{t=1}^NP(Z_{j, t}\|\theta_j)$ 。这一项的含义是对于某一个文档，求解文档中所有词的主题编号完全符合给定值的概率。如果从词语的角度看，第 $j$ 个文档中的第 $t$ 个词的主题编号为 $i$ 的概率为 $\theta_{j, i}$。因此，如果要求所有词的主题编号恰好为给定值的概率，只需要将每一个词取给定主题编号的概率相乘就行了。因此，定义 $n_{j, r}^{(i)}$ 为第 $j$ 个文档中词语编号为 $r$，且主题编号为 $i$ 的词的个数（如果某一个维度被忽略，则用 $(\cdot)$ 代替，如 $n_{j, (\cdot)}^{(i)}$ 为第 $j$ 个文档中主题编号为 $i$ 的词的个数，$n_{(\cdot), r}^{(i)}$ 为所有文档中词语编号为 $r$，且主题编号为 $i$ 的词的个数）。从上面的分析可以知道，

$$\prod_{t=1}^NP(Z_{j, t}|\theta_j) = \prod_{i=1}^K\theta_{j, i}^{n_{j, (\cdot)}^{(i)}}$$

将上式代入之前的表达式可得：

$$\int_{\theta_j}P(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta_j = \int_{\theta_j}\frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\prod_{i=1}^K\theta_{j, i}^{\alpha_i+n_{j, (\cdot)}^{(i)}-1}d\theta_j$$

可以看出，得到的表达式具有 Dirichlet 分布的形式。利用 Dirichlet 分布的归一化性质：

$$\int_{\theta_j}\frac{\Gamma(\sum_{i=1}^Kn_{j, (\cdot)}^{(i)}+\alpha_i)}{\prod_{i=1}^K\Gamma(n_{j, (\cdot)}^{(i)}+\alpha_i)}\prod_{i=1}^K\theta_{j, i}^{\alpha_i+n_{j, (\cdot)}^{(i)}-1}d\theta_j=1$$

所以有：

$$\int_{\theta_j}P(\theta_j;\alpha)\prod_{t=1}^NP(Z_{j, t}|\theta_j)d\theta_j = \int_{\theta_j}\frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\prod_{i=1}^K\theta_{j, i}^{\alpha_i+n_{j, (\cdot)}^{(i)}-1}d\theta_j = \frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\frac{\prod_{i=1}^K\Gamma(n_{j, (\cdot)}^{(i)}+\alpha_i)}{\Gamma(\sum_{i=1}^Kn_{j, (\cdot)}^{(i)}+\alpha_i)}$$

同理，可以用类似的方法得到包含 $\phi$ 的部分的积分结果：

$$\int_\phi\prod_{i=1}^KP(\phi_i;\beta)\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}})d\phi = \prod_{i=1}^K\int_{\phi_i}P(\phi_i;\beta)\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}})d\phi_i$$

$$ = \prod_{i=1}^K\int_{\phi_i}\frac{\Gamma(\sum_{r=1}^V\beta_r)}{\prod_{r=1}^V\Gamma(\beta_r)}\prod_{r=1}^V\phi_{i, r}^{\beta_r-1}\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}})d\phi_i$$

与之前的思路类似，$\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}\|\phi_{z_{j, t}})$ 代表所有文档中所有词语编号恰好符合给定值的概率；从词语的角度看的话，这个概率等价于将每一个词取给定词语编号的概率相乘：

$$
\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}}) = \prod_{r=1}^V\phi_{i, r}^{n_{(\cdot), r}^{(i)}}
$$

所以：

$$
\prod_{i=1}^K\int_{\phi_i}P(\phi_i;\beta)\prod_{j=1}^M\prod_{t=1}^NP(\omega_{j, t}|\phi_{z_{j, t}})d\phi_i = \prod_{i=1}^K\int_{\phi_i}\frac{\Gamma(\sum_{r=1}^V\beta_r)}{\prod_{r=1}^V\Gamma(\beta_r)}\prod_{r=1}^V\phi_{i, r}^{\beta_r+n_{(\cdot), r}^{(i)}-1}d\phi_i
$$

$$
 = \prod_{i=1}^K\frac{\Gamma(\sum_{r=1}^V\beta_r)}{\prod_{r=1}^V\Gamma(\beta_r)}\frac{\prod_{r=1}^V\Gamma(n_{(\cdot), r}^{(i)}+\beta_r)}{\Gamma(\sum_{r=1}^Vn_{(\cdot), r}^{(i)}+\beta_r)}
$$

接下来把上面的计算结果代入积分式，可以得到模型联合分布的解析表达式：

$$
P(\mathbf{W}, \mathbf{Z};\alpha, \beta) = \prod_{j=1}^M\frac{\Gamma(\sum_{i=1}^K\alpha_i)}{\prod_{i=1}^K\Gamma(\alpha_i)}\frac{\prod_{i=1}^K\Gamma(n_{j, (\cdot)}^{(i)}+\alpha_i)}{\Gamma(\sum_{i=1}^Kn_{j, (\cdot)}^{(i)}+\alpha_i)}\prod_{i=1}^K\frac{\Gamma(\sum_{r=1}^V\beta_r)}{\prod_{r=1}^V\Gamma(\beta_r)}\frac{\prod_{r=1}^V\Gamma(n_{(\cdot), r}^{(i)}+\beta_r)}{\Gamma(\sum_{r=1}^Vn_{(\cdot), r}^{(i)}+\beta_r)}
$$

对于 Gibbs 采样，在对某一个元素进行采样的时候，其采样表达式需要计算在已知其他所有元素时该元素的条件分布。所以在计算 Gibbs 采样表达式时，需要关注 $P(z_{m, n}\|\mathbf{Z_{-(m, n)}}, \mathbf{W};\alpha, \beta)$。另外，为了计算方便，只需要关注采样表达式的相对大小，这样对每个元素计算出相对值后进行归一化即可。

$$
P(z_{m, n} = k|\mathbf{Z_{-(m, n)}}, \mathbf{W};\alpha, \beta) \propto P(z_{m, n} = k, \mathbf{Z_{-(m, n)}}, \mathbf{W};\alpha, \beta)\propto\prod_{j=1}^M\frac{\prod_{i=1}^K\Gamma(n_{j, (\cdot)}^{(i)}+\alpha_i)}{\Gamma(\sum_{i=1}^Kn_{j, (\cdot)}^{(i)}+\alpha_i)}\prod_{i=1}^K\frac{\prod_{r=1}^V\Gamma(n_{(\cdot), r}^{(i)}+\beta_r)}{\Gamma(\sum_{r=1}^Vn_{(\cdot), r}^{(i)}+\beta_r)}
$$

$$
 = \prod_{j\ne m}\frac{\prod_{i=1}^K\Gamma(n_{j, (\cdot)}^{(i)}+\alpha_i)}{\Gamma(\sum_{i=1}^Kn_{j, (\cdot)}^{(i)}+\alpha_i)}\cdot\frac{\prod_{i=1}^K\Gamma(n_{m, (\cdot)}^{(i)}+\alpha_i)}{\Gamma(\sum_{i=1}^Kn_{m, (\cdot)}^{(i)}+\alpha_i)}\prod_{i=1}^K\frac{\prod_{r\ne v}\Gamma(n_{(\cdot), r}^{(i)}+\beta_r)}{\Gamma(\sum_{r=1}^Vn_{(\cdot), r}^{(i)}+\beta_r)}\cdot\prod_{i=1}^K\Gamma(n_{(\cdot), v}^{(i)}+\beta_v)
$$