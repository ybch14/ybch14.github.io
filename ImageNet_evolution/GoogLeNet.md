# GoogLeNet

Inception 结构的提出，ILSVRC 2014 冠军。

### 设计思路

提高深层神经网络的最直接的想法就是加大其尺寸，包括深度和宽度。但是，直接增加大小的话会有两个主要的问题：一是参数量增加，数据量有限时过拟合现象更严重；二是计算量急剧增加，而计算资源是有限的。

解决这两个问题的一个基础想法就是在网络中引入稀疏性，用更加稀疏的层代替全连接层。但是，当前的计算设备在处理非均匀分布的稀疏数据结构时效率非常低。当前在计算机视觉中，处理稀疏性的最多的解决方法是卷积。当前最好的结构都有统一的结构，卷积核非常大、batch size 也非常大，使得稠密计算能被高效地执行。

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

### GoogLeNet

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
        document.getElementById('googlenet').style.display = "block";
        document.getElementById('show-button-googlenet').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('googlenet').style.display = "none";
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
<div class="mermaid" id="googlenet" style="display: block">
    graph TD;
    image["image"];
    conv1["Conv 7k 2s"];
    pool1["MaxPool 3k 2s"];
    lrn1["LRN"];
    conv2_1["Conv 1k"];
    conv2_2["Conv 3k"];
    lrn2["LRN"];
    pool2["MaxPool 3k 2s"];
    inception1_conv1_1x1["Conv 1k"];
    inception1_conv2_1x1["Conv 1k"];
    inception1_conv3_1x1["Conv 1k"];
    inception1_conv4_1x1["Conv 1k"];
    inception1_conv3x3["Conv 3k"];
    inception1_conv5x5["Conv 5k"];
    inception1_pool3x3["MaxPool 3k"];
    inception1_concat["DepthConcat"];
    inception2_conv1_1x1["Conv 1k"];
    inception2_conv2_1x1["Conv 1k"];
    inception2_conv3_1x1["Conv 1k"];
    inception2_conv4_1x1["Conv 1k"];
    inception2_conv3x3["Conv 3k"];
    inception2_conv5x5["Conv 5k"];
    inception2_pool3x3["MaxPool 3k"];
    inception2_concat["DepthConcat"];
    pool3["MaxPool 3k 2s"];
    inception3_conv1_1x1["Conv 1k"];
    inception3_conv2_1x1["Conv 1k"];
    inception3_conv3_1x1["Conv 1k"];
    inception3_conv4_1x1["Conv 1k"];
    inception3_conv3x3["Conv 3k"];
    inception3_conv5x5["Conv 5k"];
    inception3_pool3x3["MaxPool 3k"];
    inception3_concat["DepthConcat"];
    inception4_conv1_1x1["Conv 1k"];
    inception4_conv2_1x1["Conv 1k"];
    inception4_conv3_1x1["Conv 1k"];
    inception4_conv4_1x1["Conv 1k"];
    inception4_conv3x3["Conv 3k"];
    inception4_conv5x5["Conv 5k"];
    inception4_pool3x3["MaxPool 3k"];
    inception4_concat["DepthConcat"];
    inception5_conv1_1x1["Conv 1k"];
    inception5_conv2_1x1["Conv 1k"];
    inception5_conv3_1x1["Conv 1k"];
    inception5_conv4_1x1["Conv 1k"];
    inception5_conv3x3["Conv 3k"];
    inception5_conv5x5["Conv 5k"];
    inception5_pool3x3["MaxPool 3k"];
    inception5_concat["DepthConcat"];
    inception6_conv1_1x1["Conv 1k"];
    inception6_conv2_1x1["Conv 1k"];
    inception6_conv3_1x1["Conv 1k"];
    inception6_conv4_1x1["Conv 1k"];
    inception6_conv3x3["Conv 3k"];
    inception6_conv5x5["Conv 5k"];
    inception6_pool3x3["MaxPool 3k"];
    inception6_concat["DepthConcat"];
    inception7_conv1_1x1["Conv 1k"];
    inception7_conv2_1x1["Conv 1k"];
    inception7_conv3_1x1["Conv 1k"];
    inception7_conv4_1x1["Conv 1k"];
    inception7_conv3x3["Conv 3k"];
    inception7_conv5x5["Conv 5k"];
    inception7_pool3x3["MaxPool 3k"];
    inception7_concat["DepthConcat"];
    pool4["MaxPool 3k 2s"];
    inception8_conv1_1x1["Conv 1k"];
    inception8_conv2_1x1["Conv 1k"];
    inception8_conv3_1x1["Conv 1k"];
    inception8_conv4_1x1["Conv 1k"];
    inception8_conv3x3["Conv 3k"];
    inception8_conv5x5["Conv 5k"];
    inception8_pool3x3["MaxPool 3k"];
    inception8_concat["DepthConcat"];
    inception9_conv1_1x1["Conv 1k"];
    inception9_conv2_1x1["Conv 1k"];
    inception9_conv3_1x1["Conv 1k"];
    inception9_conv4_1x1["Conv 1k"];
    inception9_conv3x3["Conv 3k"];
    inception9_conv5x5["Conv 5k"];
    inception9_pool3x3["MaxPool 3k"];
    inception9_concat["DepthConcat"];
    pool5["AvgPool 7k"];
    fc["fc"];
    softmax["SoftmaxActivation"];
    output["softmax2"];
    cls1_pool["AvgPool 5k 3s"];
    cls1_conv["Conv 1k"];
    cls1_fc1["fc"];
    cls1_fc2["fc"];
    cls1_softmax["SoftmaxActivation"];
    cls1_output["softmax0"];
    cls2_pool["AvgPool 5k 3s"];
    cls2_conv["Conv 1k"];
    cls2_fc1["fc"];
    cls2_fc2["fc"];
    cls2_softmax["SoftmaxActivation"];
    cls2_output["softmax1"];
    image --> conv1;
    conv1 --> pool1;
    pool1 --> lrn1;
    lrn1 --> conv2_1;
    conv2_1 --> conv2_2;
    conv2_2 --> lrn2;
    lrn2 --> pool2;
    pool2 --> inception1_conv1_1x1;
    pool2 --> inception1_conv2_1x1;
    pool2 --> inception1_conv3_1x1;
    pool2 --> inception1_pool3x3;
    inception1_conv2_1x1 --> inception1_conv3x3;
    inception1_conv3_1x1 --> inception1_conv5x5;
    inception1_pool3x3 --> inception1_conv4_1x1;
    inception1_conv1_1x1 --> inception1_concat;
    inception1_conv3x3 --> inception1_concat;
    inception1_conv5x5 --> inception1_concat;
    inception1_conv4_1x1 --> inception1_concat;
    inception1_concat --> inception2_conv1_1x1;
    inception1_concat --> inception2_conv2_1x1;
    inception1_concat --> inception2_conv3_1x1;
    inception1_concat --> inception2_pool3x3;
    inception2_conv2_1x1 --> inception2_conv3x3;
    inception2_conv3_1x1 --> inception2_conv5x5;
    inception2_pool3x3 --> inception2_conv4_1x1;
    inception2_conv1_1x1 --> inception2_concat;
    inception2_conv3x3 --> inception2_concat;
    inception2_conv5x5 --> inception2_concat;
    inception2_conv4_1x1 --> inception2_concat;
    pool3 --> inception3_conv1_1x1;
    pool3 --> inception3_conv2_1x1;
    pool3 --> inception3_conv3_1x1;
    pool3 --> inception3_pool3x3;
    inception3_conv2_1x1 --> inception3_conv3x3;
    inception3_conv3_1x1 --> inception3_conv5x5;
    inception3_pool3x3 --> inception3_conv4_1x1;
    inception3_conv1_1x1 --> inception3_concat;
    inception3_conv3x3 --> inception3_concat;
    inception3_conv5x5 --> inception3_concat;
    inception3_conv4_1x1 --> inception3_concat;
    inception3_concat --> inception4_conv1_1x1;
    inception3_concat --> inception4_conv2_1x1;
    inception3_concat --> inception4_conv3_1x1;
    inception3_concat --> inception4_pool3x3;
    inception4_conv2_1x1 --> inception4_conv3x3;
    inception4_conv3_1x1 --> inception4_conv5x5;
    inception4_pool3x3 --> inception4_conv4_1x1;
    inception4_conv1_1x1 --> inception4_concat;
    inception4_conv3x3 --> inception4_concat;
    inception4_conv5x5 --> inception4_concat;
    inception4_conv4_1x1 --> inception4_concat;
    inception4_concat --> inception5_conv1_1x1;
    inception4_concat --> inception5_conv2_1x1;
    inception4_concat --> inception5_conv3_1x1;
    inception4_concat --> inception5_pool3x3;
    inception5_conv2_1x1 --> inception5_conv3x3;
    inception5_conv3_1x1 --> inception5_conv5x5;
    inception5_pool3x3 --> inception5_conv4_1x1;
    inception5_conv1_1x1 --> inception5_concat;
    inception5_conv3x3 --> inception5_concat;
    inception5_conv5x5 --> inception5_concat;
    inception5_conv4_1x1 --> inception5_concat;
    inception5_concat --> inception6_conv1_1x1;
    inception5_concat --> inception6_conv2_1x1;
    inception5_concat --> inception6_conv3_1x1;
    inception5_concat --> inception6_pool3x3;
    inception6_conv2_1x1 --> inception6_conv3x3;
    inception6_conv3_1x1 --> inception6_conv5x5;
    inception6_pool3x3 --> inception6_conv4_1x1;
    inception6_conv1_1x1 --> inception6_concat;
    inception6_conv3x3 --> inception6_concat;
    inception6_conv5x5 --> inception6_concat;
    inception6_conv4_1x1 --> inception6_concat;
    inception6_concat --> inception7_conv1_1x1;
    inception6_concat --> inception7_conv2_1x1;
    inception6_concat --> inception7_conv3_1x1;
    inception6_concat --> inception7_pool3x3;
    inception7_conv2_1x1 --> inception7_conv3x3;
    inception7_conv3_1x1 --> inception7_conv5x5;
    inception7_pool3x3 --> inception7_conv4_1x1;
    inception7_conv1_1x1 --> inception7_concat;
    inception7_conv3x3 --> inception7_concat;
    inception7_conv5x5 --> inception7_concat;
    inception7_conv4_1x1 --> inception7_concat;
    inception7_concat --> pool4;
    pool4 --> inception8_conv1_1x1;
    pool4 --> inception8_conv2_1x1;
    pool4 --> inception8_conv3_1x1;
    pool4 --> inception8_pool3x3;
    inception8_conv2_1x1 --> inception8_conv3x3;
    inception8_conv3_1x1 --> inception8_conv5x5;
    inception8_pool3x3 --> inception8_conv4_1x1;
    inception8_conv1_1x1 --> inception8_concat;
    inception8_conv3x3 --> inception8_concat;
    inception8_conv5x5 --> inception8_concat;
    inception8_conv4_1x1 --> inception8_concat;
    inception8_concat --> inception9_conv1_1x1;
    inception8_concat --> inception9_conv2_1x1;
    inception8_concat --> inception9_conv3_1x1;
    inception8_concat --> inception9_pool3x3;
    inception9_conv2_1x1 --> inception9_conv3x3;
    inception9_conv3_1x1 --> inception9_conv5x5;
    inception9_pool3x3 --> inception_conv4_1x1;
    inception9_conv1_1x1 --> inception9_concat;
    inception9_conv3x3 --> inception9_concat;
    inception9_conv5x5 --> inception9_concat;
    inception9_conv4_1x1 --> inception9_concat;
    inception9_concat --> pool5;
    pool5 --> fc;
    fc --> softmax;
    softmax --> output;
    inception3_concat --> cls1_pool;
    cls1_pool --> cls1_conv;
    cls1_conv --> cls1_fc1;
    cls1_fc1 --> cls1_fc2;
    cls1_fc2 --> cls1_softmax;
    cls1_softmax --> cls1_output;
    inception6_concat --> cls2_pool;
    cls2_pool --> cls2_conv;
    cls2_conv --> cls2_fc1;
    cls2_fc1 --> cls2_fc2;
    cls2_fc2 --> cls2_softmax;
    cls2_softmax --> cls2_output;
</div>
</center>
