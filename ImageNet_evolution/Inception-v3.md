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
pool2["MaxPool k=3 s=2"];
module1_1_conv1x1["Conv k=1 BN ReLU"];
module1_1_conv5x5_a_reduce["Conv k=1 BN ReLU"];
module1_1_conv5x5_a["Conv k=5 p=2 BN ReLU"];
module1_1_pool3x3["AvgPool k=3 p=1"];
module1_1_conv3x3_b_reduce["Conv k=1 BN ReLU"];
module1_1_conv3x3_b1["Conv k=3 p=1 BN ReLU"];
module1_1_conv3x3_b2["Conv k=3 p=1 BN ReLU"];
module1_1_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module1_1_output["DepthConcat"];
module1_2_conv1x1["Conv k=1 BN ReLU"];
module1_2_conv5x5_a_reduce["Conv k=1 BN ReLU"];
module1_2_conv5x5_a["Conv k=5 p=2 BN ReLU"];
module1_2_pool3x3["AvgPool k=3 p=1"];
module1_2_conv3x3_b_reduce["Conv k=1 BN ReLU"];
module1_2_conv3x3_b1["Conv k=3 p=1 BN ReLU"];
module1_2_conv3x3_b2["Conv k=3 p=1 BN ReLU"];
module1_2_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module1_2_output["DepthConcat"];
module1_3_conv1x1["Conv k=1 BN ReLU"];
module1_3_conv5x5_a_reduce["Conv k=1 BN ReLU"];
module1_3_conv5x5_a["Conv k=5 p=2 BN ReLU"];
module1_3_pool3x3["AvgPool k=3 p=1"];
module1_3_conv3x3_b_reduce["Conv k=1 BN ReLU"];
module1_3_conv3x3_b1["Conv k=3 p=1 BN ReLU"];
module1_3_conv3x3_b2["Conv k=3 p=1 BN ReLU"];
module1_3_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module1_3_output["DepthConcat"];
reduction_a_conv3x3["Conv k=3 s=2 BN ReLU"];
reduction_a_conv3x3_a_reduce["Conv k=1 BN ReLU"];
reduction_a_conv3x3_a1["Conv k=3 p=1 BN ReLU"];
reduction_a_conv3x3_a2["Conv k=3 s=2 BN ReLU"];
reduction_a_pool3x3["MaxPool k=3 s=2"];
reduction_a_output["DepthConcat"];
module2_1_conv1x1["Conv k=1 BN ReLU"];
module2_1_convnxn_a_reduce["Conv k=1 BN ReLU"];
module2_1_convnxn_a_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_1_convnxn_a_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_1_convnxn_b_reduce["Conv k=1 BN ReLU"];
module2_1_convnxn_b1_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_1_convnxn_b1_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_1_convnxn_b2_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_1_convnxn_b2_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_1_pool3x3["AvgPool k=3 p=1"];
module2_1_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module2_1_output["DepthConcat"];
module2_2_conv1x1["Conv k=1 BN ReLU"];
module2_2_convnxn_a_reduce["Conv k=1 BN ReLU"];
module2_2_convnxn_a_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_2_convnxn_a_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_2_convnxn_b_reduce["Conv k=1 BN ReLU"];
module2_2_convnxn_b1_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_2_convnxn_b1_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_2_convnxn_b2_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_2_convnxn_b2_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_2_pool3x3["AvgPool k=3 p=1"];
module2_2_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module2_2_output["DepthConcat"];
module2_3_conv1x1["Conv k=1 BN ReLU"];
module2_3_convnxn_a_reduce["Conv k=1 BN ReLU"];
module2_3_convnxn_a_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_3_convnxn_a_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_3_convnxn_b_reduce["Conv k=1 BN ReLU"];
module2_3_convnxn_b1_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_3_convnxn_b1_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_3_convnxn_b2_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_3_convnxn_b2_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_3_pool3x3["AvgPool k=3 p=1"];
module2_3_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module2_3_output["DepthConcat"];
module2_4_conv1x1["Conv k=1 BN ReLU"];
module2_4_convnxn_a_reduce["Conv k=1 BN ReLU"];
module2_4_convnxn_a_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_4_convnxn_a_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_4_convnxn_b_reduce["Conv k=1 BN ReLU"];
module2_4_convnxn_b1_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_4_convnxn_b1_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_4_convnxn_b2_1xn["Conv k=[1,7] p=[0,3] BN ReLU"];
module2_4_convnxn_b2_nx1["Conv k=[7,1] p=[3,0] BN ReLU"];
module2_4_pool3x3["AvgPool k=3 p=1"];
module2_4_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module2_4_output["DepthConcat"];
reduction_b_conv3x3_a_reduce["Conv k=1 BN ReLU"];
reduction_b_conv3x3_a["Conv k=3 s=2 BN ReLU"];
reduction_b_conv1x7_b_reduce["Conv k=1 BN ReLU"];
reduction_b_conv1x7["Conv k=[1,7] p=[0,3] BN ReLU"];
reduction_b_conv7x1["Conv k=[7,1] p=[3,0] BN ReLU"];
reduction_b_conv3x3_b["Conv k=3 s=2 BN ReLU"];
reduction_b_pool3x3["MaxPool k=3 s=2"];
reduction_b_output["DepthConcat"];
module3_1_conv1x1["Conv k=1 BN ReLU"];
module3_1_conv3x3_a_reduce["Conv k=1 BN ReLU"];
module3_1_conv3x3_a_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
module3_1_conv3x3_a_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
module3_1_conv3x3_b_reduce["Conv k=1 BN ReLU"];
module3_1_conv3x3_b1["Conv k=3 p=1 BN ReLU"];
module3_1_conv3x3_b2_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
module3_1_conv3x3_b2_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
module3_1_pool3x3["AvgPool k=3 p=1"];
module3_1_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module3_1_output["DepthConcat"];
module3_2_conv1x1["Conv k=1 BN ReLU"];
module3_2_conv3x3_a_reduce["Conv k=1 BN ReLU"];
module3_2_conv3x3_a_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
module3_2_conv3x3_a_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
module3_2_conv3x3_b_reduce["Conv k=1 BN ReLU"];
module3_2_conv3x3_b1["Conv k=3 p=1 BN ReLU"];
module3_2_conv3x3_b2_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
module3_2_conv3x3_b2_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
module3_2_pool3x3["AvgPool k=3 p=1"];
module3_2_conv1x1_pool_proj["Conv k=1 BN ReLU"];
module3_2_output["DepthConcat"];
pool3["AvgPool k=8 dp=0.2"];
classifier["softmax classification"];
image --> |"3*299*299"| conv3x3_1;
conv3x3_1 --> |"32*149*149"| conv3x3_2;
conv3x3_2 --> |"32*147*147"| conv3x3_3;
conv3x3_3 --> |"64*147*147"| pool1;
pool1 --> |"64*73*73"| conv3x3_4_reduce;
conv3x3_4_reduce --> |"80*73*73"| conv3x3_4;
conv3x3_4 --> |"192*71*71"| pool2;
pool2 --> |"192*35*35"| module1_1_conv1x1;
pool2 --> |"192*35*35"| module1_1_conv3x3_a_reduce;
pool2 --> |"192*35*35"| module1_1_conv3x3_b_reduce;
pool2 --> |"192*35*35"| module1_1_pool3x3;
module1_1_conv5x5_a_reduce --> |"48*35*35"| module1_1_conv5x5_a;
module1_1_conv3x3_b_reduce --> |"64*35*35"| module1_1_conv3x3_b1;
module1_1_conv3x3_b1 --> |"96*35*35"| module1_1_conv3x3_b2;
module1_1_pool3x3 --> |"192*35*35"| module1_1_conv1x1_pool_proj;
module1_1_conv1x1 --> |"64*35*35"| module1_1_output;
module1_1_conv5x5_a --> |"64*35*35"| module1_1_output;
module1_1_conv3x3_b2 --> |"96*35*35"| module1_1_output;
module1_1_conv1x1_pool_proj --> |"32*35*35"| module1_1_output;
module1_1_output --> |"256*35*35"| module1_2_conv1x1;
module1_1_output --> |"256*35*35"| module1_2_conv5x5_a_reduce;
module1_1_output --> |"256*35*35"| module1_2_conv3x3_b_reduce;
module1_1_output --> |"256*35*35"| module1_2_pool3x3;
module1_2_conv5x5_a_reduce --> |"48*35*35"| module1_2_conv5x5_a;
module1_2_conv3x3_b_reduce --> |"64*35*35"| module1_2_conv3x3_b1;
module1_2_conv3x3_b1 --> |"96*35*35"| module1_2_conv3x3_b2;
module1_2_pool3x3 --> |"256*35*35"| module1_2_conv1x1_pool_proj;
module1_2_conv1x1 --> |"64*35*35"| module1_2_output;
module1_2_conv5x5_a --> |"64*35*35"| module1_2_output;
module1_2_conv3x3_b2 --> |"96*35*35"| module1_2_output;
module1_2_conv1x1_pool_proj --> |"64*35*35"| module1_2_output;
module1_2_output --> |"28*35*35"| module1_3_conv1x1;
module1_2_output --> |"28*35*35"| module1_3_conv5x5_a_reduce;
module1_2_output --> |"28*35*35"| module1_3_conv3x3_b_reduce;
module1_2_output --> |"28*35*35"| module1_3_pool3x3;
module1_3_conv5x5_a_reduce --> |"48*35*35"| module1_3_conv5x5_a;
module1_3_conv3x3_b_reduce --> |"64*35*35"| module1_3_conv3x3_b1;
module1_3_conv3x3_b1 --> |"96*35*35"| module1_3_conv3x3_b2;
module1_3_pool3x3 --> |"288*35*35"| module1_3_conv1x1_pool_proj;
module1_3_conv1x1 --> |"64*35*35"| module1_3_output;
module1_3_conv5x5_a --> |"64*35*35"| module1_3_output;
module1_3_conv3x3_b2 --> |"96*35*35"| module1_3_output;
module1_3_conv1x1_pool_proj --> |"64*35*35"| module1_3_output;
module1_3_output --> |"288*35*35"| reduction_a_conv3x3;
module1_3_output --> |"288*35*35"| reduction_a_conv3x3_a_reduce;
module1_3_output --> |"288*35*35"| reduction_a_pool3x3;
reduction_a_conv3x3_a_reduce --> |"64*35*35"| reduction_a_conv3x3_a1;
reduction_a_conv3x3_a1 --> |"93*35*35"| reduction_a_conv3x3_a2;
reduction_a_conv3x3 --> |"384*17*17"| reduction_a_output;
reduction_a_conv3x3_a2 --> |"96*17*17"| reduction_a_output;
reduction_a_pool3x3 --> |"288*17*17"| reduction_a_output;
reduction_a_output --> |"768*17*17"| module2_1_conv1x1;
reduction_a_output --> |"768*17*17"| module2_1_convnxn_a_reduce;
reduction_a_output --> |"768*17*17"| module2_1_convnxn_b_reduce;
reduction_a_output --> |"768*17*17"| module2_1_pool3x3;
module2_1_convnxn_a_reduce --> |"128*17*17"| module2_1_convnxn_a_1xn;
module2_1_convnxn_a_1xn --> |"128*17*17"| module2_1_convnxn_a_nx1;
module2_1_convnxn_b_reduce --> |"128*17*17"| module2_1_convnxn_b1_1xn;
module2_1_convnxn_b1_1xn --> |"128*17*17"| module2_1_convnxn_b1_nx1;
module2_1_convnxn_b1_nx1 --> |"128*17*17"| module2_1_convnxn_b2_1xn;
module2_1_convnxn_b2_1xn --> |"128*17*17"| module2_1_convnxn_b2_nx1;
module2_1_pool3x3 --> |"768*17*17"| module2_1_conv1x1_pool_proj;
module2_1_conv1x1 --> |"192*17*17"| module2_1_output;
module2_1_convnxn_a_nx1 --> |"192*17*17"| module2_1_output;
module2_1_convnxn_b2_nx1 --> |"192*17*17"| module2_1_output;
module2_1_conv1x1_pool_proj --> |"192*17*17"| module2_1_output;
module2_1_output --> |"768*17*17"| module2_2_conv1x1;
module2_1_output --> |"768*17*17"| module2_2_convnxn_a_reduce;
module2_1_output --> |"768*17*17"| module2_2_convnxn_b_reduce;
module2_1_output --> |"768*17*17"| module2_2_pool3x3;
module2_2_convnxn_a_reduce --> |"160*17*17"| module2_2_convnxn_a_1xn;
module2_2_convnxn_a_1xn --> |"160*17*17"| module2_2_convnxn_a_nx1;
module2_2_convnxn_b_reduce --> |"160*17*17"| module2_2_convnxn_b1_1xn;
module2_2_convnxn_b1_1xn --> |"160*17*17"| module2_2_convnxn_b1_nx1;
module2_2_convnxn_b1_nx1 --> |"160*17*17"| module2_2_convnxn_b2_1xn;
module2_2_convnxn_b2_1xn --> |"160*17*17"| module2_2_convnxn_b2_nx1;
module2_2_pool3x3 --> |"768*17*17"| module2_2_conv1x1_pool_proj;
module2_2_conv1x1 --> |"192*17*17"| module2_2_output;
module2_2_convnxn_a_nx1 --> |"192*17*17"| module2_2_output;
module2_2_convnxn_b2_nx1 --> |"192*17*17"| module2_2_output;
module2_2_conv1x1_pool_proj --> |"192*17*17"| module2_2_output;
module_2_output --> |"768*17*17"| module2_3_conv1x1;
module_2_output --> |"768*17*17"| module2_3_convnxn_a_reduce;
module_2_output --> |"768*17*17"| module2_3_convnxn_b_reduce;
module_2_output --> |"768*17*17"| module2_3_pool3x3;
module2_3_convnxn_a_reduce --> |"160*17*17"| module2_3_convnxn_a_1xn;
module2_3_convnxn_a_1xn --> |"160*17*17"| module2_3_convnxn_a_nx1;
module2_3_convnxn_b_reduce --> |"160*17*17"| module2_3_convnxn_b1_1xn;
module2_3_convnxn_b1_1xn --> |"160*17*17"| module2_3_convnxn_b1_nx1;
module2_3_convnxn_b1_nx1 --> |"160*17*17"| module2_3_convnxn_b2_1xn;
module2_3_convnxn_b2_1xn --> |"160*17*17"| module2_3_convnxn_b2_nx1;
module2_3_pool3x3 --> |"768*17*17"| module2_3_conv1x1_pool_proj;
module2_3_conv1x1 --> |"192*17*17"| module2_3_output;
module2_3_convnxn_a_nx1 --> |"192*17*17"| module2_3_output;
module2_3_convnxn_b2_nx1 --> |"192*17*17"| module2_3_output;
module2_3_conv1x1_pool_proj --> |"192*17*17"| module2_3_output;
module2_3_output --> |"768*17*17"| module2_4_conv1x1;
module2_3_output --> |"768*17*17"| module2_4_convnxn_a_reduce;
module2_3_output --> |"768*17*17"| module2_4_convnxn_b_reduce;
module2_3_output --> |"768*17*17"| module2_4_pool3x3;
module2_4_convnxn_a_reduce --> |"192*17*17"| module2_4_convnxn_a_1xn;
module2_4_convnxn_a_1xn --> |"192*17*17"| module2_4_convnxn_a_nx1;
module2_4_convnxn_b_reduce --> |"192*17*17"| module2_4_convnxn_b1_1xn;
module2_4_convnxn_b1_1xn --> |"192*17*17"| module2_4_convnxn_b1_nx1;
module2_4_convnxn_b1_nx1 --> |"192*17*17"| module2_4_convnxn_b2_1xn;
module2_4_convnxn_b2_1xn --> |"192*17*17"| module2_4_convnxn_b2_nx1;
module2_4_pool3x3 --> |"768*17*17"| module2_4_conv1x1_pool_proj;
module2_4_conv1x1 --> |"192*17*17"| module2_4_output;
module2_4_convnxn_a_nx1 --> |"192*17*17"| module2_4_output;
module2_4_convnxn_b2_nx1 --> |"192*17*17"| module2_4_output;
module2_4_conv1x1_pool_proj --> |"192*17*17"| module2_4_output;
module2_4_output --> |"768*17*17"| reduction_b_conv3x3;
module2_4_output --> |"768*17*17"| reduction_b_conv3x3_a_reduce;
module2_4_output --> |"768*17*17"| reduction_b_pool3x3;
reduction_b_conv3x3_a_reduce --> |"192*17*17"| reduction_b_conv3x3_a;
reduction_b_conv1x7_b_reduce --> |"192*17*17"| reduction_b_conv1x7;
reduction_b_conv1x7 --> |"192*17*17"| reduction_b_conv7x1;
reduction_b_conv7x1 --> |"192*17*17"| reduction_b_conv3x3_b;
reduction_b_conv3x3_a --> |"320*8*8"| reduction_b_output;
reduction_b_conv3x3_b --> |"192*8*8"| reduction_b_output;
reduction_b_pool3x3 --> |"768*8*8"| reduction_b_output;
reduction_b_output --> |"1280*8*8"| module3_1_conv1x1;
reduction_b_output --> |"1280*8*8"| module3_1_conv3x3_a_reduce;
reduction_b_output --> |"1280*8*8"| module3_1_conv3x3_b_reduce;
reduction_b_output --> |"1280*8*8"| module3_1_pool3x3;
module3_1_conv3x3_a_reduce --> |"384*8*8"| module3_1_conv3x3_a_1x3;
module3_1_conv3x3_a_reduce --> |"384*8*8"| module3_1_conv3x3_a_3x1;
module3_1_conv3x3_b_reduce --> |"448*8*8"| module3_1_conv3x3_b1;
module3_1_conv3x3_b1 --> |"384*8*8"| module3_1_conv3x3_b2_1x3;
module3_1_conv3x3_b1 --> |"384*8*8"| module3_1_conv3x3_b2_3x1;
module3_1_pool3x3 --> |"1280*8*8"| module3_1_conv1x1_pool_proj;
module3_1_conv1x1 --> |"320*8*8"| module3_1_output;
module3_1_conv3x3_a_1x3 --> |"384*8*8"| module3_1_output;
module3_1_conv3x3_a_3x1 --> |"384*8*8"| module3_1_output;
module3_1_conv3x3_b2_1x3 --> |"384*8*8"| module3_1_output;
module3_1_conv3x3_b2_3x1 --> |"384*8*8"| module3_1_output;
module3_1_conv1x1_pool_proj --> |"192*8*8"| module3_1_output;
module3_1_output --> |"2048*8*8"| module3_2_conv1x1;
module3_1_output --> |"2048*8*8"| module3_2_conv3x3_a_reduce;
module3_1_output --> |"2048*8*8"| module3_2_conv3x3_b_reduce;
module3_1_output --> |"2048*8*8"| module3_2_pool3x3;
module3_2_conv3x3_a_reduce --> |"384*8*8"| module3_2_conv3x3_a_1x3;
module3_2_conv3x3_a_reduce --> |"384*8*8"| module3_2_conv3x3_a_3x1;
module3_2_conv3x3_b_reduce --> |"448*8*8"| module3_2_conv3x3_b1;
module3_2_conv3x3_b1 --> |"384*8*8"| module3_2_conv3x3_b2_1x3;
module3_2_conv3x3_b1 --> |"384*8*8"| module3_2_conv3x3_b2_3x1;
module3_2_pool3x3 --> |"2048*8*8"| module3_2_conv1x1_pool_proj;
module3_2_conv1x1 --> |"320*8*8"| module3_2_output;
module3_2_conv3x3_a_1x3 --> |"384*8*8"| module3_2_output;
module3_2_conv3x3_a_3x1 --> |"384*8*8"| module3_2_output;
module3_2_conv3x3_b2_1x3 --> |"384*8*8"| module3_2_output;
module3_2_conv3x3_b2_3x1 --> |"384*8*8"| module3_2_output;
module3_2_conv1x1_pool_proj --> |"192*8*8"| module3_2_output;
module3_2_output --> |"2048*8*8"| pool3;
pool3 --> |"2048"|classifier;
</div>
</center>

### 参考文献

1. C. Szegedy, V. Vanhoucke, S. Ioffe, J. Shlens, and Z. Wojna. Rethinking the inception architecture for computer vision. arXiv preprint arXiv:1512.00567, 2015.