# AlexNet

第一个用于 ImageNet 分类的深度网络，ILSVRC 2012 / 2013 冠军采用的架构。Top-5 错误率 15.3%，吊打第二的 26.2%。

### 网络结构

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton()
{
    if (is_show == false)
    {
        document.getElementById('mermaid-graph').style.display = "block";
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('mermaid-graph').style.display = "none";
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button" onclick="ClickShowButton()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="mermaid-graph" style="display: block">
    graph TD;
    image["Image"];
    conv1["Conv [k = 11, s = 4, c = 96] + ReLU + LRN"];
    pool1["Max Pooling [k = 3, s = 2]"];
    conv2["Conv [k = 5, s = 1, p = 2, c = 256] + ReLU + LRN"];
    pool2["Max Pooling [k = 3, s = 2]"];
    conv3["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"];
    conv4["Conv [k = 3, s = 1, p = 1, c = 384] + ReLU"];
    conv5["Conv [k = 3, s = 1, p = 1, c = 256] + ReLU"];
    pool5["Max Pooling [k = 3, s = 2]"];
    fc6["fc [o = 4096]"];
    fc7["fc [o = 4096]"];
    fc8["fc [o = 1000]"];
    pred["ImageNet classification result"];
    image -->|"3 * 224 * 224"| conv1;
    conv1 -->|"96 * 54 * 54"| pool1;
    pool1 -->|"96 * 27 * 27"| conv2;
    conv2 -->|"256 * 27 * 27"| pool2;
    pool2 -->|"256 * 13 * 13"| conv3;
    conv3 -->|"384 * 13 * 13"| conv4;
    conv4 -->|"384 * 13 * 13"| conv5;
    conv5 -->|"256 * 13 * 13"| pool5;
    pool5 -->|"256 * 6 * 6"| fc6;
    fc6 -->|"4096"| fc7;
    fc7 -->|"4096"| fc8;
    fc8 -->|"1000"| pred;
</div>
</center>

### ReLU

传统激活函数（ReLU，$tanh$）都有存在饱和的问题。因此采用一种比 SGD 收敛更快的激活函数 $f(x) = \max (0, x)$，称为 ReLU（修正线性单元，Rectified Linear Units）。实验证明使用 ReLU 后网络的收敛速度为原来的7倍。

### LRN

ReLU 激活函数的有点是不需要因为避免饱和而对输入数据做归一化。但是，作者发现下面的局部归一化方法能够得到更好的泛化能力。记 $a_{x, y}^i$为第 $i$ 个卷积核在 $(x, y)$ 位置作用并经过 ReLU 后得到的输出，则归一化表达式为：

$$
b_{x, y}^i = a_{x, y}^i / \left(k+\alpha\sum_{j = \max(0, i-n/2)}^{\min(N-1, i+n/2)}(a_{x, y}^j)\right)^\beta
$$

其中的求和是在相同的空间位置，跨越 $n$ 个相邻的特征图求和；$N$ 是该层的卷积核个数。特征图的顺序无所谓，在训练之前就已经确定。这种归一化方式使得不同特征图之间的较大的激活值能够形成竞争。常数 $k, n, \alpha, \beta$ 为超参数，在验证集上确定：$k=2, n=5, \alpha = 10^{-4}, \beta = 0.75$。

### 减少过拟合

#### 数据增强

实验中采用两种保留标签的变换增强方式：切割（+ 翻转）和改变亮度。切割是指从 $256 \times 256$ 的图片中切割出 $224 \times 224$ 大小的图片，再加上横向翻转，一共对数据扩展 $32 \times 32 \times 2 = 2048$ 倍。改变亮度是指对训练集中所有图片 RGB 通道的像素值做 PCA，然后对所有图片的三个通道分别叠加主成分，具体地在图片上叠加如下的数值：

$$
[p_1, p_2, p_3][\alpha_1\lambda_1, \alpha_2\lambda_2, \alpha_3\lambda_3]^T
$$

其中 $p_i, \lambda_i$ 是 RGB 像素值的 $3\times 3$ 协方差矩阵的第 $i$ 个特征向量和特征值，$\alpha_i$ 是零均值、方差 0.1 的高斯随机变量。

#### Dropout

- Dropout 是指随机对每个隐藏层的神经元输出以 0.5 的概率置零。被置零的神经元在前向通路和后向通路中都不起作用。Dropout 的方法能够减少神经元中复杂的自适应现象，使得网络能够学习更多有用的连接关系。测试时，我们使用所有的神经元，对其输出值乘以 0.5，作为对所有置零后的子网络中各个神经元输出值期望的合理估计。

- 实验中，对前两个全连接层采用 Dropout，虽然收敛时间加长了一倍，但极大减少了过拟合的现象。

### 实验结果

|    Model    |  Top-1  |  Top-5  |
|:-----------:|:-------:|:-------:|
|Sparse coding|  47.1%  |  28.2%  |
|  SIFT + FVs |  45.7%  |  25.7%  |
|     CNN     |**37.5%**|**17.0%**|