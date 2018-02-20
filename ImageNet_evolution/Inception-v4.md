# Inception v4

- 对 v3 进一步加深和优化，提高性能。
- v3 中不同 Inception 模块的超参数（卷积层输出通道）都不同。在 v4 中对这一点做了改进，对每种尺寸的特征图采用统一的超参数。

### 总体结构图

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
        document.getElementById('inception-v4-graph').style.display = "block";
        document.getElementById('show-button-inception-v4').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-v4-graph').style.display = "none";
        document.getElementById('show-button-inception-v4').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-v4" onclick="ClickShowButton1()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-v4-graph" style="display: block">
graph TD;
image["Image"];
stem["Stem"];
inception_a["4 x Inception-A"];
reduction_a["Reduction-A"];
inception_b["7 x Inception-B"];
reduction_b["Reduction-B"];
inception_c["3 x Inception-C"];
pool["AvgPool k=8 dp=0.2"];
classifier["softmax classifier"];
image --> |"3*299*299"| stem;
stem --> |"384*35*35"| inception_a;
inception_a --> |"384*35*35"| reduction_a;
reduction_a --> |"1024*17*17"| inception_b;
inception_b --> |"1024*17*17"| reduction_b;
reduction_b --> |"1536*8*8"| inception_c;
inception_c --> |"1536*8*8"| pool;
pool --> |"1536"| classifier;
</div>
</center>

### 内部模块

- Stem

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
        document.getElementById('stem-graph').style.display = "block";
        document.getElementById('show-button-stem').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('stem-graph').style.display = "none";
        document.getElementById('show-button-stem').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-stem" onclick="ClickShowButton1()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="stem-graph" style="display: block">
graph TD;
input["Input"];
conv1["Conv k=3 s=2 BN ReLU"];
conv2["Conv k=3 BN ReLU"];
conv3["Conv k=3 p=1 BN ReLU"];
conv4["Conv k=3 s=2 BN ReLU"]
pool4["MaxPool k=3 p=2"];
concat4["DepthConcat"];
conv5_3x3_a_reduce["Conv k=1 BN ReLU"];
conv5_3x3_a["Conv k=3 BN ReLU"];
conv5_3x3_b_reduce["Conv k=1 BN ReLU"];
conv5_1x7_b["Conv k=[1,7] p=[0,3] BN ReLU"];
conv5_7x1_b["Conv k=[7,1] p=[3,0] BN ReLU"];
conv5_3x3_b["Conv k=3 BN ReLU"];
concat5["DepthConcat"];
conv6["Conv k=3 s=2 BN ReLU"];
pool6["MaxPool k=3 s=2"];
concat6["DepthConcat"];
output["Output"];
input --> |"3*299*299"| conv1;
conv1 --> |"32*149*149"| conv2;
conv2 --> |"32*147*147"| conv3;
conv3 --> |"64*147*147"| conv4;
conv3 --> |"64*147*147"| pool4;
conv4 --> |"96*73*73"| concat4;
pool4 --> |"64*73*73"| concat4;
concat4 --> |"160*73*73"| conv5_3x3_a_reduce;
conv5_3x3_a_reduce --> |64*73*73| conv5_3x3_a;
conv5_3x3_a --> |"96*71*71"| concat5;
concat4 --> |"160*73*73"| conv5_3x3_b_reduce;
conv5_3x3_b_reduce --> |"64*73*73"| conv5_1x7_b;
conv5_1x7_b --> |"64*73*73"| conv5_7x1_b;
conv5_7x1_b --> |"64*73*73"| conv5_3x3_b;
conv5_3x3_b --> |"96*71*71"| concat5;
concat5 --> |"192*71*71"| conv6;
concat5 --> |"192*71*71"| pool6;
conv6 --> |"192*35*35"| concat6;
pool6 --> |"192*35*35"| concat6;
concat6 --> |"384*35*35"| output;
</div>
</center>

- Inception A

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButtonA()
{
    if (is_show == false)
    {
        document.getElementById('inception-a-graph').style.display = "block";
        document.getElementById('show-button-inception-a').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-a-graph').style.display = "none";
        document.getElementById('show-button-inception-a').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-a" onclick="ClickShowButtonA()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-a-graph" style="display: block">
graph TD;
base["Input"];
conv1x1["Conv k=1 BN ReLU"];
conv3x3_a_reduce["Conv k=1 BN ReLU"];
conv3x3_a["Conv k=3 p=1 BN ReLU"];
pool3x3["AvgPool k=3 p=1"];
conv3x3_b_reduce["Conv k=1 BN ReLU"];
conv3x3_b1["Conv k=3 p=1 BN ReLU"];
conv3x3_b2["Conv k=3 p=1 BN ReLU"];
conv1x1_pool_proj["Conv k=1 BN ReLU"];
concat["DepthConcat"];
output["Output"];
base --> |"384*35*35"| conv1x1;
base --> |"384*35*35"| conv3x3_a_reduce;
base --> |"384*35*35"| conv3x3_b_reduce;
base --> |"384*35*35"| pool3x3;
conv3x3_a_reduce --> |"64*35*35"| conv3x3_a;
conv3x3_b_reduce --> |"64*35*35"| conv3x3_b1;
conv3x3_b1 --> |"96*35*35"| conv3x3_b2;
pool3x3 --> |"384*35*35"| conv1x1_pool_proj;
conv1x1 --> |"96*35*35"| concat;
conv3x3_a --> |"96*35*35"| concat;
conv3x3_b2 --> |"96*35*35"| concat;
conv1x1_pool_proj --> |"96*35*35"| concat;
concat --> |"384*35*35"| output;
</div>
</center>

- Inception B

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButtonB()
{
    if (is_show == false)
    {
        document.getElementById('inception-b-graph').style.display = "block";
        document.getElementById('show-button-inception-b').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-b-graph').style.display = "none";
        document.getElementById('show-button-inception-b').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-b" onclick="ClickShowButtonB()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-b-graph" style="display: block">
graph TD;
base["Input"];
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
concat["DepthConcat"];
output["Output"];
base --> |"1024*17*17"| conv1x1;
base --> |"1024*17*17"| convnxn_a_reduce;
base --> |"1024*17*17"| convnxn_b_reduce;
base --> |"1024*17*17"| pool3x3;
convnxn_a_reduce --> |"192*17*17"| convnxn_a_1xn;
convnxn_a_1xn --> |"224*17*17"| convnxn_a_nx1;
convnxn_b_reduce --> |"192*17*17"| convnxn_b1_1xn;
convnxn_b1_1xn --> |"192*17*17"| convnxn_b1_nx1;
convnxn_b1_nx1 --> |"224*17*17"| convnxn_b2_1xn;
convnxn_b2_1xn --> |"224*17*17"| convnxn_b2_nx1;
pool3x3 --> |"1024*17*17"| conv1x1_pool_proj;
conv1x1 --> |"384*17*17"| concat;
convnxn_a_nx1 --> |"256*17*17"| concat;
convnxn_b2_nx1 --> |"256*17*17"| concat;
conv1x1_pool_proj --> |"128*17*17"| concat;
concat --> |"1024*17*17"| output;
</div>
</center>

- Inception C

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButtonC()
{
    if (is_show == false)
    {
        document.getElementById('inception-c-graph').style.display = "block";
        document.getElementById('show-button-inception-c').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-c-graph').style.display = "none";
        document.getElementById('show-button-inception-c').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-c" onclick="ClickShowButtonC()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-c-graph" style="display: block">
graph TD;
base["Input"];
conv1x1["Conv k=1 BN ReLU"];
conv3x3_a_reduce["Conv k=1 BN ReLU"];
conv3x3_a_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
conv3x3_a_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
conv3x3_b_reduce["Conv k=1 BN ReLU"];
conv3x3_b1_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
conv3x3_b1_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
conv3x3_b2_1x3["Conv k=[1,3] p=[0,1] BN ReLU"];
conv3x3_b2_3x1["Conv k=[3,1] p=[1,0] BN ReLU"];
pool3x3["AvgPool k=3 p=1"];
conv1x1_pool_proj["Conv k=1 BN ReLU"];
concat["DepthConcat"];
output["Output"];
base --> |"1536*8*8"| conv1x1;
base --> |"1536*8*8"| conv3x3_a_reduce;
base --> |"1536*8*8"| conv3x3_b_reduce;
base --> |"1536*8*8"| pool3x3;
conv3x3_a_reduce --> |"384*8*8"| conv3x3_a_1x3;
conv3x3_a_reduce --> |"384*8*8"| conv3x3_a_3x1;
conv3x3_b_reduce --> |"384*8*8"| conv3x3_b1_1x3;
conv3x3_b1_1x3 --> |"448*8*8"| conv3x3_b1_3x1;
conv3x3_b1_3x1 --> |"512*8*8"| conv3x3_b2_1x3;
conv3x3_b1_3x1 --> |"512*8*8"| conv3x3_b2_3x1;
pool3x3 --> |"1536*8*8"| conv1x1_pool_proj;
conv1x1 --> |"256*8*8"| concat;
conv3x3_a_1x3 --> |"256*8*8"| concat;
conv3x3_a_3x1 --> |"256*8*8"| concat;
conv3x3_b2_1x3 --> |"256*8*8"| concat;
conv3x3_b2_3x1 --> |"256*8*8"| concat;
conv1x1_pool_proj --> |"256*8*8"| concat;
concat --> |"1536*8*8"| output;
</div>
</center>

- Reduction A

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButtonRA()
{
    if (is_show == false)
    {
        document.getElementById('reduction-a-graph').style.display = "block";
        document.getElementById('show-button-reduction-a').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('reduction-a-graph').style.display = "none";
        document.getElementById('show-button-reduction-a').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-reduction-a" onclick="ClickShowButtonRA()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="reduction-a-graph" style="display: block">
graph TD;
input["Input"];
conv_3x3_a["Conv k=3 s=2 BN ReLU"];
conv_3x3_b_reduce["Conv k=1 BN ReLU"];
conv_3x3_b1["Conv k=3 p=1 BN ReLU"];
conv_3x3_b2["Conv k=3 s=2 BN ReLU"];
pool["MaxPool k=3 s=2"];
concat["DepthConcat"];
output["Output"];
input --> |"384*35*35"| conv_3x3_a;
input --> |"384*35*35"| conv_3x3_b_reduce;
input --> |"384*35*35"| pool;
pool --> |"384*17*17"| concat;
conv_3x3_a --> |"384*17*17"| concat;
conv_3x3_b_reduce --> |"192*35*35"| conv_3x3_b1;
conv_3x3_b1 --> |"224*35*35"| conv_3x3_b2;
conv_3x3_b2 --> |"256*17*17"| concat;
concat --> |"1024*17*17"| output;
</div>
</center>

- Reduction B

<script type="text/javascript" src="../js/mermaid.js"></script>
<script type="text/javascript">
mermaid.initialize({startOnLoad:true});
</script>
<script type="text/javascript">
var is_show = true;
function ClickShowButtonRB()
{
    if (is_show == false)
    {
        document.getElementById('reduction-b-graph').style.display = "block";
        document.getElementById('show-button-reduction-b').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('reduction-b-graph').style.display = "none";
        document.getElementById('show-button-reduction-b').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-reduction-b" onclick="ClickShowButtonRB()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="reduction-b-graph" style="display: block">
graph TD;
input["Input"];
conv_3x3_a_reduce["Conv k=1 BN ReLU"];
conv_3x3_a["Conv k=3 s=2 BN ReLU"];
conv_3x3_b_reduce["Conv k=1 BN ReLU"];
conv_1x7_b["Conv k=[1,7] p=[0,3] BN ReLU"];
conv_7x1_b["Conv k=[7,1] p=[3,0] BN ReLU"];
conv_3x3_b["Conv k=3 s=3 BN ReLU"];
pool["MaxPool k=3 s=2"];
concat["DepthConcat"];
output["Output"];
input --> |"1024*17*17"| conv_3x3_a_reduce;
conv_3x3_a_reduce --> |"192*17*17"| conv_3x3_a;
conv_3x3_a --> |"192*8*8"| concat;
conv_3x3_b_reduce --> |"256*17*17"| conv_1x7_b;
conv_1x7_b --> |"256*17*17"| conv_7x1_b;
conv_7x1_b --> |"320*17*17"| conv_3x3_b;
conv_3x3_b --> |"320*8*8"| concat;
concat --> |"1536*8*8"| output;
</div>
</center>

### 实验结果

- ILSVRC 2012 上 144 切割的实验结果

|Network|Crops|Top-1 Error |Top-5 Error|
|:-:|:-:|:-:|:-:|
|ResNet-151|dense|19.4%|4.5%|
|Inception-v3|144|18.9%|4.3%|
|Inception-v4|144|17.7%|3.8%|