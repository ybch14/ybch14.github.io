# Inception v3

### 改进

- 引入分解的思想，将 $k \times k$ 卷积分解成 $1 \times k + k \times 1$ 的卷积，既可以加速计算，又可以增加网络深度，提高非线性。

### Inception 模块

#### Module 1

- 和 Inception v2 的基本模块相同，把 $5 \times 5$ 的卷积分解成两个连续的 $3 \times 3$ 卷积。这种 Inception 模块作用于 $35 \times 35$ 的特征图上。

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
        document.getElementById('inception-1-graph').style.display = "block";
        document.getElementById('show-button-inception-1').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-1-graph').style.display = "none";
        document.getElementById('show-button-inception-1').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-1" onclick="ClickShowButton1()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-1-graph" style="display: block">
graph TD;
base["base"];
conv1x1["Conv k=1 BN ReLU"];
conv3x3_a_reduce["Conv k=1 BN ReLU"];
conv3x3_a["Conv k=3 p=1 BN ReLU"];
pool3x3["AvgPool k=3 p=1"];
conv3x3_b_reduce["Conv k=1 BN ReLU"];
conv3x3_b1["Conv k=3 p=1 BN ReLU"];
conv3x3_b2["Conv k=3 p=1 BN ReLU"];
conv1x1_pool_proj["Conv k=1 BN ReLU"];
output["DepthConcat"];
base --> conv1x1;
base --> conv3x3_a_reduce;
base --> conv3x3_b_reduce;
base --> pool3x3;
conv3x3_a_reduce --> conv3x3_a;
conv3x3_b_reduce --> conv3x3_b1;
conv3x3_b1 --> conv3x3_b2;
pool3x3 --> conv1x1_pool_proj;
conv1x1 --> output;
conv3x3_a --> output;
conv3x3_b2 --> output;
conv1x1_pool_proj --> output;
</div>
</center>

#### Module 2

- 将 $n\times n$ 卷积分解成 $1\times n$ 和 $n\times 1$ 的卷积。这种 Inception 模块作用于 $17 \times 17$ 的特征图上，$n=7$。

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
        document.getElementById('inception-2-graph').style.display = "block";
        document.getElementById('show-button-inception-2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-2-graph').style.display = "none";
        document.getElementById('show-button-inception-2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-2" onclick="ClickShowButton2()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-2-graph" style="display: block">
graph TD;
base["base"];
conv1x1["Conv k=1 BN ReLU"];
convnxn_a_reduce["Conv k=1 BN ReLU"];
convnxn_a_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
convnxn_a_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
convnxn_b_reduce["Conv k=1 BN ReLU"];
convnxn_b1_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
convnxn_b1_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
convnxn_b2_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
convnxn_b2_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
pool3x3["AvgPool k=3 p=1"];
conv1x1_pool_proj["Conv k=1 BN ReLU"];
output["DepthConcat"];
base --> conv1x1;
base --> convnxn_a_reduce;
base --> convnxn_b_reduce;
base --> pool3x3;
convnxn_a_reduce --> convnxn_a_1xn;
convnxn_a_1xn --> convnxn_a_nx1;
convnxn_b_reduce --> convnxn_b1_1xn;
convnxn_b1_1xn --> convnxn_b1_nx1;
convnxn_b1_nx1 --> convnxn_b2_1xn;
convnxn_b2_1xn --> convnxn_b2_nx1;
pool3x3 --> conv1x1_pool_proj;
conv1x1 --> output;
convnxn_a_nx1 --> output;
convnxn_b2_nx1 --> output;
conv1x1_pool_proj --> output;
</div>
</center>

#### Module 3

- 将最高层的 $3\times 3$ 卷积分解成平行的 $1\times 3$ 和 $3 \times 1$ 卷积，为了是平衡 Inception Module 2 增加的网络深度。

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
        document.getElementById('inception-3-graph').style.display = "block";
        document.getElementById('show-button-inception-3').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-3-graph').style.display = "none";
        document.getElementById('show-button-inception-3').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-3" onclick="ClickShowButton3()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-3-graph" style="display: block">
graph TD;
base["base"];
conv1x1["Conv k=1 BN ReLU"];
conv3x3_a_reduce["Conv k=1 BN ReLU"];
conv3x3_a_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
conv3x3_a_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
conv3x3_b_reduce["Conv k=1 BN ReLU"];
conv3x3_b1["Conv k=3 p=1 BN ReLU"];
conv3x3_b2_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
conv3x3_b2_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
pool3x3["AvgPool k=3 p=1"];
conv1x1_pool_proj["Conv k=1 BN ReLU"];
output["DepthConcat"];
base --> conv1x1;
base --> conv3x3_a_reduce;
base --> conv3x3_b_reduce;
base --> pool3x3;
conv3x3_a_reduce --> conv3x3_a_1x3;
conv3x3_a_reduce --> conv3x3_a_3x1;
conv3x3_b_reduce --> conv3x3_b1;
conv3x3_b1 --> conv3x3_b2_1x3;
conv3x3_b1 --> conv3x3_b2_3x1;
pool3x3 --> conv1x1_pool_proj;
conv1x1 --> output;
conv3x3_a_1x3 --> output;
conv3x3_a_3x1 --> output;
conv3x3_b2_1x3 --> output;
conv3x3_b2_3x1 --> output;
conv1x1_pool_proj --> output;
</div>
</center>

### Reduction 模块

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton4()
{
    if (is_show == false)
    {
        document.getElementById('reduction-graph').style.display = "block";
        document.getElementById('show-button-reduction').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('reduction-graph').style.display = "none";
        document.getElementById('show-button-reduction').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-reduction" onclick="ClickShowButton3()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="reduction-graph" style="display: block">
graph TD;
base["base"];
conv3x3["Conv k=3 s=2 BN ReLU"];
conv3x3_a_reduce["Conv k=1 BN ReLU"];
conv3x3_a1["Conv k=3 p=1 BN ReLU"];
conv3x3_a2["Conv k=3 s=2 BN ReLU"];
pool3x3["MaxPool k=3 s=2"];
output["DepthConcat"];
base --> conv3x3;
base --> conv3x3_a_reduce;
base --> pool3x3;
conv3x3_a_reduce --> conv3x3_a1;
conv3x3_a1 --> conv3x3_a2;
conv3x3 --> output;
conv3x3_a2 --> output;
pool3x3 --> output;
</div>
</center>

### Inception v3 结构

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButton4()
{
    if (is_show == false)
    {
        document.getElementById('inception-v3-graph').style.display = "block";
        document.getElementById('show-button-inception-v3').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-v3-graph').style.display = "none";
        document.getElementById('show-button-inception-v3').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-v3" onclick="ClickShowButton4()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-v3-graph" style="display: block">
graph TD;
image["image"];
conv3x3_1["Conv k=3 s=2 BN ReLU"];
conv3x3_2["Conv k=3 BN ReLU"];
conv3x3_3["Conv k=3 p=1 BN ReLU"];
pool1["MaxPool k=3 s=2"];
conv3x3_4_reduce["Conv k=1 BN ReLU"]
conv3x3_4["Conv k=3 BN ReLU"];  
pool2["MaxPool k=3 p=2"]
Module1_1["Module 1"];
Module1_2["Module 1"];
Module1_3["Module 1"];
Reduction_1["Reduction"];
Module2_1["Module 2"];
Module2_2["Module 2"];
Module2_3["Module 2"];
Module2_4["Module 2"];
Reduction_2["Reduction"];
Module3_1["Module 3"];
Module3_2["Module 3"];
pool3["AvgPool k=8 dp=0.2"];
classifier["softmax classification"];
image --> |"3*299*299"| conv3x3_1;
conv3x3_1 --> |"32*149*149"| conv3x3_2;
conv3x3_2 --> |"32*147*147"| conv3x3_3;
conv3x3_3 --> |"64*147*147"| pool1;
pool1 --> |"64*73*73"| conv3x3_4_reduce;
conv3x3_4_reduce --> |"80*73*73"| conv3x3_4;
conv3x3_4 --> |"192*71*71"| pool2;
pool2 --> |"192*35*35"| Module1_1;
Module1_1 --> |"256*35*35"| Module1_2;
Module1_2 --> |"288*35*35"| Module1_3;
Module1_3 --> |"288*35*35"| Reduction_1;
Reduction_1 --> |"768*17*17"| Module2_1;
Module2_1 --> |"768*17*17"| Module2_2;
Module2_2 --> |"768*17*17"| Module2_3;
Module2_3 --> |"768*17*17"| Module2_4;
Module2_4 --> |"768*17*17"| Reduction_2;
Reduction_2 --> |"1280*8*8"| Module3_1;
Module3_1 --> |"2048*8*8"| Module3_2;
Module3_2 --> |"2048*8*8"| pool3;
pool3 --> |"2048"|classifier;
</div>
</center>

### 参考文献

1. C. Szegedy, V. Vanhoucke, S. Ioffe, J. Shlens, and Z. Wojna. Rethinking the inception architecture for computer vision. arXiv preprint arXiv:1512.00567, 2015.