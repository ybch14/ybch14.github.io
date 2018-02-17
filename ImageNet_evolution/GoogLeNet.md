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
    conv1 --> |"64*112*112"| pool1;
    pool1 --> |"64*56*56"| lrn1;
    lrn1 -->  |"64*56*56"|conv2_1;
    conv2_1 --> |"64*56*56"| conv2_2;
    conv2_2 --> |"192*56*56"| lrn2;
    lrn2 --> |"192*56*56"| pool2;
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
