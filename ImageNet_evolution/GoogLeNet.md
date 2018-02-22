# GoogLeNet

Inception 结构的提出，ILSVRC 2014 冠军。

### 设计思路

提高深层神经网络的最直接的想法就是加大其尺寸，包括深度和宽度。但是，直接增加大小的话会有两个主要的问题：一是参数量增加，数据量有限时过拟合现象更严重；二是计算量急剧增加，而计算资源是有限的。

解决这两个问题的一个基础想法就是在网络中引入稀疏性，用更加稀疏的层代替全连接层。但是，当前的计算设备在处理非均匀分布的稀疏数据结构时效率非常低。当前在计算机视觉中，处理稀疏性的最常见解决方法是卷积。当前最好的网络结构都很统一：卷积核非常大、batch size 也非常大，使得稠密计算能被高效地执行。

这带来了一个问题：能否有一种结构能够利用卷积核层级的稀疏性，同时利用当前计算设备的稠密计算性能。前人工作证明对稀疏矩阵聚类变为稠密子矩阵的方法能够在稀疏矩阵相乘的任务中有很好的表现。这种系数矩阵聚类的方法也可以用在网络结构的设计上。这就是 Inception 模块的设计思路。

### Inception 模块

- Naive 版本

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton1()
{
    if (is_show == false)
    {
        document.getElementById('naive-inception').style.display = "block";
        document.getElementById('show-button-naive-inception').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('naive-inception').style.display = "none";
        document.getElementById('show-button-naive-inception').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-naive-inception" onclick="ClickShowButton1()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="naive-inception" style="display: block">
    graph TD;
    previous["Previous Layer"];
    conv1x1["1x1 convolution"];
    conv3x3["3x3 convolution"];
    conv5x5["5x5 convolution"];
    pool3x3["3x3 max pooling"];
    filter["Filter Concatenation"];
    previous --> conv1x1;
    previous --> conv3x3;
    previous --> conv5x5;
    previous --> pool3x3;
    conv1x1 --> filter;
    conv3x3 --> filter;
    conv5x5 --> filter;
    pool3x3 --> filter;
</div>
</center> 

- 问题：在输入通道数较多时，$5\times 5$ 卷积的计算消耗很大。另外，由于模块中含有 pooling 部分，其输出通道数和输入通道数相同，因此整个 Inception 模块的输出通道数必然大于输入通道数。这使得当很多 Inception 模块堆叠起来的时候，特征图通道数会迅速增加，使得整个网络结构的计算效率非常低。

- 改进版本：用 $1\times 1$ 卷积降低维度，减少计算量。

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton2()
{
    if (is_show == false)
    {
        document.getElementById('dimreduct-inception').style.display = "block";
        document.getElementById('show-button-dimreduct-inception').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('dimreduct-inception').style.display = "none";
        document.getElementById('show-button-dimreduct-inception').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-dimreduct-inception" onclick="ClickShowButton2()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="dimreduct-inception" style="display: block">
    graph TD;
    previous["Previous Layer"];
    conv1_1x1["1x1 convolution"];
    conv2_1x1["1x1 convolution"];
    conv3_1x1["1x1 convolution"];
    conv4_1x1["1x1 convolution"];
    conv3x3["3x3 convolution"];
    conv5x5["5x5 convolution"];
    pool3x3["3x3 max pooling"];
    filter["Filter Concatenation"];
    previous --> conv1_1x1;
    previous --> conv2_1x1;
    previous --> conv3_1x1;
    previous --> pool3x3;
    conv2_1x1 --> conv3x3;
    conv3_1x1 --> conv5x5;
    pool3x3 --> conv4_1x1;
    conv1_1x1 --> filter;
    conv3x3 --> filter;
    conv5x5 --> filter;
    conv4_1x1 --> filter;
</div>
</center>

- 通常，Inception 网络指的就是包含（堆叠的）Inception 模块的网络。处于显存的考虑，在网络的低层可以使用传统的卷积网络，在更上层使用 Inception 模块。
- Inception 网络结构的优点是在每个阶段可以显著增加神经元的数目，而不会导致网络后序部分的计算复杂度爆炸。另外这种设计也符合视觉直觉：视觉信息应当在不同的尺度进行处理之后融合，以便下一个阶段可以统一提取不同尺度的特征。

### GoogLeNet 结构

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton3()
{
    if (is_show == false)
    {
        document.getElementById('googlenet-graph').style.display = "block";
        document.getElementById('show-button-googlenet').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('googlenet-graph').style.display = "none";
        document.getElementById('show-button-googlenet').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-googlenet" onclick="ClickShowButton3()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="googlenet-graph" style="display: block">
    graph TD;
    image["image"];
    conv1["Conv k=7 s=2 p=3"];
    pool1["MaxPool k=3 s=2"];
    lrn1["LRN"];
    conv2_1["Conv k=1"];
    conv2_2["Conv k=3 p=1"];
    lrn2["LRN"];
    pool2["MaxPool k=3 s=2"];
    inception1_conv1_1x1["Conv k=1"];
    inception1_conv2_1x1["Conv k=1"];
    inception1_conv3_1x1["Conv k=1"];
    inception1_conv4_1x1["Conv k=1"];
    inception1_conv3x3["Conv k=3 p=1"];
    inception1_conv5x5["Conv k=5 p=2"];
    inception1_pool3x3["MaxPool k=3 p=1"];
    inception1_concat["DepthConcat"];
    inception2_conv1_1x1["Conv k=1"];
    inception2_conv2_1x1["Conv k=1"];
    inception2_conv3_1x1["Conv k=1"];
    inception2_conv4_1x1["Conv k=1"];
    inception2_conv3x3["Conv k=3 p=1"];
    inception2_conv5x5["Conv k=5 p=2"];
    inception2_pool3x3["MaxPool k=3 p=1"];
    inception2_concat["DepthConcat"];
    pool3["MaxPool k=3 s=2"];
    inception3_conv1_1x1["Conv k=1"];
    inception3_conv2_1x1["Conv k=1"];
    inception3_conv3_1x1["Conv k=1"];
    inception3_conv4_1x1["Conv k=1"];
    inception3_conv3x3["Conv k=3 p=1"];
    inception3_conv5x5["Conv k=5 p=2"];
    inception3_pool3x3["MaxPool k=3 p=1"];
    inception3_concat["DepthConcat"];
    inception4_conv1_1x1["Conv k=1"];
    inception4_conv2_1x1["Conv k=1"];
    inception4_conv3_1x1["Conv k=1"];
    inception4_conv4_1x1["Conv k=1"];
    inception4_conv3x3["Conv k=3 p=1"];
    inception4_conv5x5["Conv k=5 p=2"];
    inception4_pool3x3["MaxPool k=3 p=1"];
    inception4_concat["DepthConcat"];
    inception5_conv1_1x1["Conv k=1"];
    inception5_conv2_1x1["Conv k=1"];
    inception5_conv3_1x1["Conv k=1"];
    inception5_conv4_1x1["Conv k=1"];
    inception5_conv3x3["Conv k=3 p=1"];
    inception5_conv5x5["Conv k=5 p=2"];
    inception5_pool3x3["MaxPool k=3 p=1"];
    inception5_concat["DepthConcat"];
    inception6_conv1_1x1["Conv k=1"];
    inception6_conv2_1x1["Conv k=1"];
    inception6_conv3_1x1["Conv k=1"];
    inception6_conv4_1x1["Conv k=1"];
    inception6_conv3x3["Conv k=3 p=1"];
    inception6_conv5x5["Conv k=5 p=2"];
    inception6_pool3x3["MaxPool k=3 p=1"];
    inception6_concat["DepthConcat"];
    inception7_conv1_1x1["Conv k=1"];
    inception7_conv2_1x1["Conv k=1"];
    inception7_conv3_1x1["Conv k=1"];
    inception7_conv4_1x1["Conv k=1"];
    inception7_conv3x3["Conv k=3 p=1"];
    inception7_conv5x5["Conv k=5 p=2"];
    inception7_pool3x3["MaxPool k=3 p=1"];
    inception7_concat["DepthConcat"];
    pool4["MaxPool k=3 s=2"];
    inception8_conv1_1x1["Conv k=1"];
    inception8_conv2_1x1["Conv k=1"];
    inception8_conv3_1x1["Conv k=1"];
    inception8_conv4_1x1["Conv k=1"];
    inception8_conv3x3["Conv k=3 p=1"];
    inception8_conv5x5["Conv k=5 p=2"];
    inception8_pool3x3["MaxPool k=3 p=1"];
    inception8_concat["DepthConcat"];
    inception9_conv1_1x1["Conv k=1"];
    inception9_conv2_1x1["Conv k=1"];
    inception9_conv3_1x1["Conv k=1"];
    inception9_conv4_1x1["Conv k=1"];
    inception9_conv3x3["Conv k=3 p=1"];
    inception9_conv5x5["Conv k=5 p=2"];
    inception9_pool3x3["MaxPool k=3 p=1"];
    inception9_concat["DepthConcat"];
    pool5["AvgPool k=7"];
    fc["fc"];
    softmax["SoftmaxActivation"];
    output["softmax2"];
    cls1_pool["AvgPool k=5 s=3"];
    cls1_conv["Conv k=1"];
    cls1_fc1["fc"];
    cls1_fc2["fc"];
    cls1_softmax["SoftmaxActivation"];
    cls1_output["softmax0"];
    cls2_pool["AvgPool k=5 s=3"];
    cls2_conv["Conv k=1"];
    cls2_fc1["fc"];
    cls2_fc2["fc"];
    cls2_softmax["SoftmaxActivation"];
    cls2_output["softmax1"];
    image --> |"3*224*224"| conv1;
    subgraph stem;
        conv1 --> |"64*112*112"| pool1;
        pool1 --> |"64*56*56"| lrn1;
        lrn1 -->  |"64*56*56"|conv2_1;
        conv2_1 --> |"64*56*56"| conv2_2;
        conv2_2 --> |"192*56*56"| lrn2;
        lrn2 --> |"192*56*56"| pool2;
    end;
    pool2 --> |"192*28*28"| inception1_conv1_1x1;
    pool2 --> |"192*28*28"| inception1_conv2_1x1;
    pool2 --> |"192*28*28"| inception1_conv3_1x1;
    pool2 --> |"192*28*28"| inception1_pool3x3;
    inception1_conv2_1x1 --> |"96*28*28"| inception1_conv3x3;
    inception1_conv3_1x1 --> |"16*28*28"| inception1_conv5x5;
    inception1_pool3x3 --> |"192*28*28"| inception1_conv4_1x1;
    inception1_conv1_1x1 --> |"64*28*28"| inception1_concat;
    inception1_conv3x3 --> |"128*28*28"| inception1_concat;
    inception1_conv5x5 --> |"32*28*28"| inception1_concat;
    inception1_conv4_1x1 --> |"32*28*28"| inception1_concat;
    inception1_concat --> |"256*28*28"| inception2_conv1_1x1;
    inception1_concat --> |"256*28*28"| inception2_conv2_1x1;
    inception1_concat --> |"256*28*28"| inception2_conv3_1x1;
    inception1_concat --> |"256*28*28"| inception2_pool3x3;
    inception2_conv2_1x1 --> |"128*28*28"| inception2_conv3x3;
    inception2_conv3_1x1 --> |"32*28*28"| inception2_conv5x5;
    inception2_pool3x3 --> |"256*28*28"| inception2_conv4_1x1;
    inception2_conv1_1x1 --> |"128*28*28"|inception2_concat;
    inception2_conv3x3 --> |"192*28*28"| inception2_concat;
    inception2_conv5x5 --> |"96*28*28"| inception2_concat;
    inception2_conv4_1x1 --> |"64*28*28"| inception2_concat;
    inception2_concat --> |"480*28*28"| pool3;
    pool3 --> |"480*14*14"| inception3_conv1_1x1;
    pool3 --> |"480*14*14"| inception3_conv2_1x1;
    pool3 --> |"480*14*14"| inception3_conv3_1x1;
    pool3 --> |"480*14*14"| inception3_pool3x3;
    inception3_conv2_1x1 --> |"96*14*14"| inception3_conv3x3;
    inception3_conv3_1x1 --> |"16*14*14"| inception3_conv5x5;
    inception3_pool3x3 --> |"480*14*14"| inception3_conv4_1x1;
    inception3_conv1_1x1 --> |"192*14*14"| inception3_concat;
    inception3_conv3x3 --> |"208*14*14"| inception3_concat;
    inception3_conv5x5 --> |"48*14*14"| inception3_concat;
    inception3_conv4_1x1 --> |"64*14*14"| inception3_concat;
    inception3_concat --> |"512*14*14"| inception4_conv1_1x1;
    inception3_concat --> |"512*14*14"| inception4_conv2_1x1;
    inception3_concat --> |"512*14*14"| inception4_conv3_1x1;
    inception3_concat --> |"512*14*14"| inception4_pool3x3;
    inception4_conv2_1x1 --> |"112*14*14"| inception4_conv3x3;
    inception4_conv3_1x1 --> |"24*14*14"| inception4_conv5x5;
    inception4_pool3x3 --> |"512*14*14"| inception4_conv4_1x1;
    inception4_conv1_1x1 --> |"160*14*14"| inception4_concat;
    inception4_conv3x3 --> |"224*14*14"| inception4_concat;
    inception4_conv5x5 --> |"64*14*14"| inception4_concat;
    inception4_conv4_1x1 --> |"64*14*14"| inception4_concat;
    inception4_concat --> |"512*14*14"| inception5_conv1_1x1;
    inception4_concat --> |"512*14*14"| inception5_conv2_1x1;
    inception4_concat --> |"512*14*14"| inception5_conv3_1x1;
    inception4_concat --> |"512*14*14"| inception5_pool3x3;
    inception5_conv2_1x1 --> |"128*14*14"| inception5_conv3x3;
    inception5_conv3_1x1 --> |"24*14*14"| inception5_conv5x5;
    inception5_pool3x3 --> |"512*14*14"| inception5_conv4_1x1;
    inception5_conv1_1x1 --> |"128*14*14"| inception5_concat;
    inception5_conv3x3 --> |"256*14*14"| inception5_concat;
    inception5_conv5x5 --> |"64*14*14"| inception5_concat;
    inception5_conv4_1x1 --> |"64*14*14"| inception5_concat;
    inception5_concat --> |"512*14*14"| inception6_conv1_1x1;
    inception5_concat --> |"512*14*14"| inception6_conv2_1x1;
    inception5_concat --> |"512*14*14"| inception6_conv3_1x1;
    inception5_concat --> |"512*14*14"| inception6_pool3x3;
    inception6_conv2_1x1 --> |"144*14*14"| inception6_conv3x3;
    inception6_conv3_1x1 --> |"32*14*14"| inception6_conv5x5;
    inception6_pool3x3 --> |"512*14*14"| inception6_conv4_1x1;
    inception6_conv1_1x1 --> |"112*14*14"| inception6_concat;
    inception6_conv3x3 --> |"288*14*14"| inception6_concat;
    inception6_conv5x5 --> |"64*14*14"| inception6_concat;
    inception6_conv4_1x1 --> |"64*14*14"| inception6_concat;
    inception6_concat --> |"528*14*14"| inception7_conv1_1x1;
    inception6_concat --> |"528*14*14"| inception7_conv2_1x1;
    inception6_concat --> |"528*14*14"| inception7_conv3_1x1;
    inception6_concat --> |"528*14*14"| inception7_pool3x3;
    inception7_conv2_1x1 --> |"160*14*14"| inception7_conv3x3;
    inception7_conv3_1x1 --> |"32*14*14"| inception7_conv5x5;
    inception7_pool3x3 --> |"528*14*14"| inception7_conv4_1x1;
    inception7_conv1_1x1 --> |"256*14*14"| inception7_concat;
    inception7_conv3x3 --> |"320*14*14"| inception7_concat;
    inception7_conv5x5 --> |"128*14*14"| inception7_concat;
    inception7_conv4_1x1 --> |"128*14*14"| inception7_concat;
    inception7_concat --> |"832*14*14"| pool4;
    pool4 --> |"832*7*7"| inception8_conv1_1x1;
    pool4 --> |"832*7*7"| inception8_conv2_1x1;
    pool4 --> |"832*7*7"| inception8_conv3_1x1;
    pool4 --> |"832*7*7"| inception8_pool3x3;
    inception8_conv2_1x1 --> |"160*7*7"| inception8_conv3x3;
    inception8_conv3_1x1 --> |"32*7*7"| inception8_conv5x5;
    inception8_pool3x3 --> |"832*7*7"| inception8_conv4_1x1;
    inception8_conv1_1x1 --> |"256*7*7"| inception8_concat;
    inception8_conv3x3 --> |"320*7*7"| inception8_concat;
    inception8_conv5x5 --> |"128*7*7"| inception8_concat;
    inception8_conv4_1x1 --> |"128*7*7"| inception8_concat;
    inception8_concat --> |"832*7*7"| inception9_conv1_1x1;
    inception8_concat --> |"832*7*7"| inception9_conv2_1x1;
    inception8_concat --> |"832*7*7"| inception9_conv3_1x1;
    inception8_concat --> |"832*7*7"| inception9_pool3x3;
    inception9_conv2_1x1 --> |"192*7*7"| inception9_conv3x3;
    inception9_conv3_1x1 --> |"48*7*7"| inception9_conv5x5;
    inception9_pool3x3 --> |"832*7*7"| inception9_conv4_1x1;
    inception9_conv1_1x1 --> |"384*7*7"| inception9_concat;
    inception9_conv3x3 --> |"384*7*7"| inception9_concat;
    inception9_conv5x5 --> |"128*7*7"| inception9_concat;
    inception9_conv4_1x1 --> |"128*7*7"| inception9_concat;
    inception9_concat --> |"1024*7*7"| pool5;
    pool5 --> |"1024*1*1"| fc;
    fc --> |"1000"| softmax;
    softmax --> |"1000"| output;
    inception3_concat --> |"512*14*14"| cls1_pool;
    cls1_pool --> |"512*4*4"| cls1_conv;
    cls1_conv --> |"128*4*4"| cls1_fc1;
    cls1_fc1 --> |"1024"| cls1_fc2;
    cls1_fc2 --> |"1000"| cls1_softmax;
    cls1_softmax --> |"1000"| cls1_output;
    inception6_concat --> |"528*14*14"| cls2_pool;
    cls2_pool --> |"528*4*4"| cls2_conv;
    cls2_conv --> |"128*4*4"| cls2_fc1;
    cls2_fc1 --> |"1024"| cls2_fc2;
    cls2_fc2 --> |"1000"| cls2_softmax;
    cls2_softmax --> |"1000"| cls2_output;
</div>
</center>

- 所有卷积层的激活函数都是 ReLU。
- 对于一个相对来说深度比较大的网络，梯度信号如何有效反传是一个问题。对于分类问题，浅层网络的表现证明在网络中间层的特征应该非常具有分辨力。在中间层上加入辅助分类器可以加强更浅层的分辨力，抵消梯度消失问题。这些辅助分类器就是小的卷积网络。训练过程中辅助分类器的损失函数乘以折扣因子后加在总损失函数上。在测试过程中辅助分类器忽略。
- 分类器结构：
    - average pooling, $5 \times 5$ 卷积核，跨距为3
    - $1 \times 1$ 卷积核，128 通道输出，ReLU 激活函数。
    - 全卷积层，1024 个神经元，ReLU 激活函数。
    - dropout，0.7。
    - softmax 分类器，输出 1000 分类分数。

### 训练和测试方法

- 1200000 训练，50000 验证，100000 测试。
- 独立训练 7 个相同模型，进行模型融合。
- 图片切割：把图片缩放到短边为 256, 288, 320, 352，取左、中、右三个正方形区域（竖直图片取上中下）。对每个正方形区域取 4 个角和中间 $224 \times 224$ 的区域、正方形区域直接缩放为 $224 \times 224$，以及上述所有正方形区域的翻转。这样每张图片取 $4 \times 3 \times 6 \times 2 = 144$ 个区域。
- softmax 的输出概率在多个切割区域和所有的分类器上取平均。

### 实验结果

- 和其他方法对比

|Team|Year|Place|Error (top-5)|Uses external data|
|:-:|:-:|:-:|:-:|:-:|
|SuperVision|2012|1st|16.4%|no|
|SuperVision|2012|1st|15.3%|ImageNet 22k|
|Clarifai|2013|1st|11.7%|no|
|Clarifai|2013|1st|11.2%|ImageNet 22k|
|MSRA|2014|3rd|7.35%|no|
|VGG|2014|2nd|7.32%|no|
|**GoogLeNet**|**2014**|**1st**|**6.67%**|**no**|

- 超参数对比

|Number of models|Number of Crops|Cost|Top-5 error|
|:-:|:-:|:-:|:-:|
|1|1|1|10.07%|
|1|10|10|9.15%|
|1|144|144|7.89%|
|7|1|7|8.09%|
|7|10|70|7.62%|
|7|144|1008|6.67%|

### 参考文献

1. Szegedy, Christian, Liu, Wei, Jia, Yangqing, Sermanet, Pierre, Reed, Scott, Anguelov, Dragomir, Erhan, Dumitru, Vanhoucke, Vincent, and Rabinovich, Andrew. Going deeper with convolutions. CoRR, abs/1409.4842, 2014.