# Fast R-CNN

R-CNN 的扩展，比 R-CNN 快 200+ 倍。

- 借鉴 SPP-net 的思路，采用 RoI pooling 层（只有一个 level 的 SPP-net，见 SPP-net）。
- Multi-task training，同时实现分类和 bounding box regression。

### 网络结构

- 输入为一整张图像和若干候选框， 图像经过深层卷积网络提取特征图（VGG-16），RoI 经过映射之后得到特征图的 RoI （方法见 SPP-net Section 6）。
- 每个 RoI 由四个数定义：左上角坐标（r, c）、高度（h）、宽度（w）。
- RoI pooling 层是一个只有一个 level 的，子区域数目固定的 SPP 层。因此 RoI 层的输出维数是固定的。RoI pooling 层把 $h \times w$ 的候选窗口分割成 $H \times W$ 个子窗口，并对每个子窗口做 pooling。
- RoI pooling 层得到的输出经过一系列全连接层之后得到 RoI 特征向量。
- RoI 特征向量分别经过两个全连接层，分别接入 softmax 分类器和 bounding box regressor 得到训练结果。

### 训练方法

- R-CNN 和 SPPnet 的训练方法有缺点：这两者的训练方法是从所有图片的所有 RoI 中统一采样构成 batch。这可能导致每个训练样本都来自不同的图片。而每个 RoI 都会有很大的感受野，因此网络训练的前馈阶段有可能会处理非常大的图片，导致训练性能下降。如果一个 batch 里面的训练样本都来自同的图片，那么每张图片都要过一遍卷积网络，速度非常慢。

- 新的训练方法：层次式采样，先对图片采样，然后对选中图片的 RoI 采样。比如对一个 128 的 batch，可以先采样得到两张图片，然后对每张图片随机选择64个候选框。这样做的好处是在每个 batch 里只需要过两次卷积层，训练速度会提升很多。虽然这种方法可能导致不收敛，但是在实际训练的时候没发现。

- softmax 分类输出一个 K+1 维的概率向量 $p$，用于对 K 类训练数据做分类

- bounding box regressor 的输出是一个四维向量 $t^k = (t_x^k, t_y^k, t_w^k, t_h^k)$ ，其中

  - $P^k = (P_x^k, P_y^k, P_w^k, P_h^k)$ 是网络输出的候选框的中心点坐标+长度+宽度
  - $G^k = (G_x^k, G_y^k, G_w^k, G_h^k)$ 是真实值的候选框的中心点坐标+长度+宽度
  - $t_x^k = (G_x^k - P_x^k)/P_w^k$
  - $t_y^k = (G_y^k - P_y^k)/P_h^k$
  - $t_w^k = \log(G_w^k/P_w^k)$
  - $t_h^k=\log(G_h^k/P_h^k)$

  这样做是为了保证 bounding box regression 对缩放变换和长宽比变换的不变性。

- 每个训练样本（RoI）带有一个类别标签 $u$ 和一个 bounding box 的真值 $v$ 。定义如下多任务 loss 函数：

  - $L(p, u; t^u, v) = L_{cls}(p, u) + \lambda[u\ge 1]L_{loc}(t^u, v)$，其中 $L_{cls}(p, u) = -\log p_u$，$[u \ge 1]$ 意为当 $u \ge 1$ 时取值为1，否则取值为0（即对背景不做 bounding box regression）。
  - $L_{loc}(t^u, v) = \sum_{i \in \{x, y, w, h\}}smooth_{L_1}(t_i^u - v_i)$
  - $smooth_{L_1}(x) = \begin{cases}0.5x^2 & if\ \|x\|<1 \\\\ \|x\| - 0.5 & otherwise \end{cases}$
  - $L_1$ loss 可以减轻梯度爆炸的问题

### 测试方法

- 如果做图片分类，测试的主要时间开销在卷积层；如果做物体检测，由于一张图片只做一次卷积，而 RoI 的个数很多，每个都要经过一次全连接，所以主要的时间开销在全连接计算。
- 全连接的计算可以用奇异值分解（SVD）的方法进行加速：
  - $W \approx U \Sigma_tV^T$，$W \in \mathbb{R}^{u\times v}, \ U \in \mathbb{R}^{u \times t}, \ \Sigma_t \in \mathbb{R}^{t \times t}, \ V \in \mathbb{R}^{v \times t}$
  - 参数个数从 $uv$ 降为 $t(u+v)$ 。如果 $t$ 远小于 $\min(u, v)$ ，参数数量会大幅降低。
  - 为了压缩网络，用两个全连接层代替原来的一个全连接层：一个代表 $\Sigma_tV^T$ （不加偏置），一个代表 $U$ （偏置和原全连接层相同）

### 结果

- state of the art mAP (平均准确率) on VOC07, 2010 and 2012
- 与 R-CNN 和 SPPnet 相比训练和测试速度都有很大提高
- 在 VGG16 上微调卷积层参数，imporve mAP。



### 参考文献 

1. R. Girshick. Fast R-CNN. In ICCV, 2015