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
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('naive-inception').style.display = "none";
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
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

- 改进版本：维度降低

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
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('dimreduct-inception').style.display = "none";
        document.getElementById('show-button').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
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

