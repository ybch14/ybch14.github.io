# Feature Pyramid Network

特征金字塔网络，目的是利用卷积网络中不同尺度的特征图，因为不同尺度的特征图包含不同的语义含义。网络的目的是为了构建带有高层语义含义的特征金字塔，集成不同尺度特征图中的语义信息，增强网络的学习效果。

### FPN

- FPN 的输入为任意大小的图片，输出大小成比例的不同层级的特征图。
- 构建特征图的过程与提取特征的 backbone 卷积网络独立。论文总采用的卷积网络是 ResNet。
- 网络的结构包含如下几个部分：
  - 自底向上的通路：卷积网络（backbone）的前向计算通路。计算得到层次结构的特征图，每层之间尺度的缩放系数为2。在网络中有很多连续的特征图尺寸相同，把这些连续的特征图称为一段（stage）。对于特征金字塔中的特征图，每一段被定义为一级。我们选择每一段的最后一层特征图作为特征金字塔的参考特征图，因为在一级中最深层的特征图通常具有最代表性的特征。对于 ResNet 来说，选择每段的最后一个 residual block 的输出作为特征图。把 ResNet 中每个阶段的输出记作 $\\{C_2, C_3, C_4, C_5\\}$，这些特征图相对原图来说跨距为 {4, 8, 16, 32} 。
  - 自顶向下的通路：在自顶向下的通路中，下一层的特征图是从上一层特征图进行升采样得到的。除了升采样之外，在自顶向下的通路和自底向上的通路中还存在横向连接：每个横向连接把两个通路中相同大小的特征图融合在一起（自底向上特征图 --> 1x1 conv + 2倍升采样{上层的自顶向下特征图}）。虽然自底向上的特征图特征含义比较弱，但是其经过升采样的次数比较少，位置更精确。自顶向下通路的生成过程是一个迭代过程：在自顶向下的通路中最高层级（尺寸最小）的特征图是从自底向上通路中最小的特征图经过 1x1 的卷积层之后得到的；之后按上面的方法迭代逐层构建通路，直至得到的特征图和自底向上通路中最大的特征图制度相同为止。迭代结束之后自顶向下通路中所有的特征图再经过一个 3x3 的卷积层，用于消除升采样的混叠效应。最终得到的自顶向下通路中的特征图记作 $\\{P_2, P_3, P_4, P_5\\}$。
  - 对于传统深层卷积网络，不同层次之间的特征图语义含义有非常大的差别，而自顶向下的通路通过升采样和横向连接的方法融合语义信息，减小不同层级之间的语义差别。而如果没有横向连接的话，特征金字塔中的特征图经过反复降采样和升采样，物体位置可能不准确。横向连接的作用就是把位置信息比较精确但是语义信息不强（自底向上通路中）的特征图和语义信息强但位置不精确（自顶向下通路中）的特征图融合在一起，互相抵消负面影响。
  - 网络在自顶向下通路中的每一级都会有预测输出（分类标签/box  regression 输出）。由于分类器和回归器的参数都是共享的，所以自顶向下通路中每一层的特征图都应该具有相同的通道数。论文中取 256 个通道。在自顶向下通路中所有的层都不引入非线性激活函数。
  - 文章中提到，这篇文章的目的是提出一种带有横向连接的特征金字塔的结构。至于横向连接的具体形式不做深究。实际上采用一个 residual block 代替上文提到的 1x1 卷积层会有更好的结果，但是不属于这篇文章的讨论范围。从特征图的角度看，特征金字塔把强语义信息的特征图方法，并充分利用各层级的语义信息；从 RPN 的角度看，RPN 的输入为固定窗口大小，因此采用多尺度的 FPN 结构能够提高 RPN 对于物体尺度缩放的鲁棒性。

### FPN 的应用

- FPN for RPN (Region Proposal Network)
  - RPN 是一个在卷积网络最后一层特征图上滑动的小网络，包含一个 3x3 的卷积层和两个 1x1 的卷积层，称为 *head*。​其分类标准和 bounding box 回归的都是建立在 anchor 的概念之上（见 Faster R-CNN）。
  - 在 RPN 中使用 FPN 的方法很简单，就是用特征金字塔结构代替卷积网络最后一层的特征图。将 head 网络（3x3 卷积和 2 个 1x1 卷积）应用到特征金字塔自顶向下通路的所有特征图中（所有特征图共用一个 head 网络）。由于 head
     网络在所有（不同尺度的）特征图上的所有位置上滑动，所以没有必要对每一个特征图都选择多个尺度的 anchor，在一层中使用一个尺度的 anchor 就可以。论文中的做法是在 $\\{P_2, P_3, P_4, P_5, P_6\\}$ 上分别使用 $\\{32^2, 64^2, 128^2, 256^2, 512^2\\}$ 大小的 anchor（$P_6$ 是 $P_5$ 的简单 2 倍下采样，仅仅为了引入 $512^2$ 的 anchor，不在 Fast R-CNN 中使用）。长宽比还是和 Faster R-CNN 中一样，为 $\\{1:2, 1:1, 2:1\\}$。对整个特征金字塔，一共有 15 种不同的 anchor。
  - anchor 的训练标签分配方法和 Faster R-CNN 相同（见 Faster R-CNN）。
- FPN for Fast R-CNN
  - Fast R-CNN 是一种基于 RoI 的物体检测方法。想要在 Fast R-CNN 里应用 FPN ，需要把不同尺度的 RoI 分配到不同的金字塔层级上。
  - 分配策略如下：如果 RoI 的高度和宽度分别是 $w$ 和 $h$ ，那么这个 RoI 被分配到的金字塔层级为 $P_k$，

  $$k = \lfloor k_0 + \log_2(\sqrt{wh}/224) \rfloor$$

  - 类似基于 ResNet 的 Faster R-CNN （见 ResNet）中使用的 $C_4$ 特征图，此处令 $k_0 = 4$。对所有层级上的所有 RoI 应用 head 网络（所有 RoI 共享 head 网络的权重）。在基于 ResNet 的 Faster R-CNN 中，head 网络使用的是 ResNet 的 conv5 （一个9层的子网络），应用在 conv4 的特征图上。但是在 FPN 方法中 conv5 已经在构建特征金字塔的时候用过了，所以此处的 head 网络为两个 1024 长的全连接层：经过 RoIPooling 之后提取的 7*7 的特征输入到这两个全连接层中，其输出结果输入到分类器和回归器中得到预测结果。

### 实验结果

- 对 RPN 使用 FPN （AR, average recall）

|  RPN   |     特征图      | \#anchor | 横向连接？ | 自顶向下？ | AR$^{100}$ | AR$^{1k}$ |
| :----: | :------------: | :------: | :---: | :---: | :--------: | :-------: |
|  无FPN  |    $C_4$     |   47k    |       |       |    36.1    |   48.3    |
|  无FPN  |    $C_5$     |   12k    |       |       |    36.3    |   44.9    |
|  FPN   | $\\{P_k \\}$ |   200k   |  yes  |  yes  |  **44.0**  | **56.3**  |
|  对照实验  |              |          |       |       |            |           |
| 无自顶向下  | $\\{P_k \\}$ |   200k   |  yes  |       |    37.4    |   49.5    |
| 无横向连接  | $\\{P_k \\}$ |   200k   |       |  yes  |    34.5    |   48.1    |
| 无金字塔结构 |    $P_2$     |   750k   |  yes  |  yes  |    38.4    |   51.3    |

- 对 Fast R-CNN 使用 FPN（AP, average precision）

| Fast R-CNN |       候选框        |     特征图      | head  | 横向连接？ | 自顶向下？ |  AP$_s$  |  AP$_m$  |  AP$_l$  |
| :--------: | :--------------: | :----------: | :---: | :---: | :---: | :------: | :------: | :------: |
|    无FPN    | RPN, $\\{P_k\\}$ |    $C_4$     | conv5 |       |       |   15.7   |   36.5   |   45.5   |
|    无FPN    | RPN, $\\{P_k\\}$ |    $C_5$     |  2fc  |       |       |   11.9   |   32.4   |   43.4   |
|    FPN     | RPN, $\\{P_k\\}$ | $\\{P_k \\}$ |  2fc  |  yes  |  yes  | **17.8** | **37.7** | **45.8** |
|    对照实验    |                  |              |       |       |       |          |          |          |
|   无自顶向下    | RPN, $\\{P_k\\}$ | $\\{P_k \\}$ |  2fc  |  yes  |       |   10.9   |   24.4   |   38.5   |
|   无横向连接    | RPN, $\\{P_k\\}$ | $\\{P_k \\}$ |  2fc  |       |  yes  |   13.3   |   35.2   |   45.3   |
|   无金字塔结构   | RPN, $\\{P_k\\}$ |    $P_2$     |  2fc  |  yes  |  yes  |   17.3   |   37.3   |   45.6   |

- 和 Faster R-CNN 比较


| Faster R-CNN |       候选框        |     特征图     | head  | 横向连接？ | 自顶向下？ |  AP$_s$  |  AP$_m$  |  AP$_l$  |
| :----------: | :--------------: | :---------: | :---: | :---: | :---: | :------: | :------: | :------: |
|     无FPN     |    RPN, $C_4$    |    $C_4$    | conv5 |       |       |   13.2   |   35.6   | **47.1** |
|     无FPN     |    RPN, $C_5$    |    $C_5$    |  2fc  |       |       |   9.6    |   31.9   |   43.1   |
|     FPN      | RPN, $\\{P_k\\}$ | $\\{P_k\\}$ |  2fc  |  yes  |  yes  | **17.8** | **37.7** |   45.8   |



### 参考文献

1. T. -Y. Lin, P. Dollar, R. Girshick, K. He, B. Hariharan, and S. Belongie. Feature pyramid networks for object detection. In CVPR, 2017.