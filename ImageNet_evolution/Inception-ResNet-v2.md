# Inception-ResNet v2

与 Inception-ResNet v1 一起提出，结构上比 v1 更大，准确率更高。

### 总体结构

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
        document.getElementById('inception-resnet-v2-graph').style.display = "block";
        document.getElementById('show-button-inception-resnet-v2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-resnet-v2-graph').style.display = "none";
        document.getElementById('show-button-inception-resnet-v2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-resnet-v2" onclick="ClickShowButton1()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-resnet-v2-graph" style="display: block">
graph TD;
image["Image"];
stem["Stem"];
inception_a["5 x Inception-ResNet-A"];
reduction_a["Reduction-A"];
inception_b["10 x Inception-ResNet-B"];
reduction_b["Reduction-B"];
inception_c["5 x Inception-ResNet-C"];
pool["AvgPool k=8"];
dropout["Dropout 0.2"];
classifier["softmax classifier"];
image --> |"3*299*299"| stem;
stem --> |"384*35*35"| inception_a;
inception_a --> |"384*35*35"| reduction_a;
reduction_a --> |"1152*17*17"| inception_b;
inception_b --> |"1152*17*17"| reduction_b;
reduction_b --> |"2144*8*8"| inception_c;
inception_c --> |"2144*8*8"| pool;
pool --> |"2144"| dropout;
pool --> |"2144"| classifier;
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
function ClickShowButtonS()
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
<center><button class="button show" id="show-button-stem" onclick="ClickShowButtonS()">
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

- Inception-ResNet-A

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
input["Input"];
conv_1x1["Conv k=1 ReLU"];
conv_3x3_a_reduce["Conv k=1 ReLU"];
conv_3x3_a["Conv k=3 p=1 ReLU"];
conv_3x3_b_reduce["Conv k=1 ReLU"];
conv_3x3_b1["Conv k=3 p=1 ReLU"];
conv_3x3_b2["Conv k=3 p=1 ReLU"];
concat["DepthConcat"];
concat_proj["Conv k=1"];
sum["Sum"];
bn["BN ReLU"];
output["Output"];
input --> |"384*35*35"| conv_1x1;
conv_1x1 --> |"32*35*35"| concat;
input --> |"384*35*35"| conv_3x3_a_reduce;
conv_3x3_a_reduce --> |"32*35*35"| conv_3x3_a;
conv_3x3_a --> |"32*35*35"| concat;
input --> |"384*35*35"| conv_3x3_b_reduce;
conv_3x3_b_reduce --> |"32*35*35"| conv_3x3_b1;
conv_3x3_b1 --> |"48*35*35"| conv_3x3_b2;
conv_3x3_b2 --> |"64*35*35"| concat;
concat --> |"128*35*35"| concat_proj;
concat_proj --> |"384*35*35"| sum;
input --> |"384*35*35"| sum;
sum --> |"384*35*35"| bn;
bn --> |"384*35*35"| output;
</div>
</center>

- Inception-ResNet-B

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
input["Input"];
conv_1x1["Conv k=1 ReLU"];
conv_reduce["Conv k=1 ReLU"];
conv_1x7["Conv k=[1,7] p=[0,3] ReLU"];
conv_7x1["Conv k=[7,1] p=[3,0] ReLU"];
concat["DepthConcat"];
concat_proj["Conv k=1"];
sum["Sum"];
bn["BN ReLU"];
output["Output"];
input --> |"1152*17*17"| conv_1x1;
conv_1x1 --> |"192*17*17"| concat;
input --> |"1152*17*17"| conv_reduce;
conv_reduce --> |"128*17*17"| conv_1x7;
conv_1x7 --> |"160*17*17"| conv_7x1;
conv_7x1 --> |"192*17*17"| concat;
concat --> |"384*17*17"| concat_proj;
concat_proj --> |"1152*17*17"| sum;
input --> |"1152*17*17"| sum;
sum --> |"1152*17*17"| bn;
bn --> |"1152*17*17"| output;
</div>
</center>

- Inception-ResNet-C

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
input["Input"];
conv_1x1["Conv k=1 ReLU"];
conv_reduce["Conv k=1 ReLU"];
conv_1x3["Conv k=[1,3] p=[0,1] ReLU"];
conv_3x1["Conv k=[3,1] p=[1,0] ReLU"];
concat["DepthConcat"];
concat_proj["Conv k=1"];
sum["Sum"];
bn["BN ReLU"];
output["Output"];
input --> |"2144*8*8"| conv_1x1;
conv_1x1 --> |"192*8*8"| concat;
input --> |"2144*8*8"| conv_reduce;
conv_reduce --> |"192*8*8"| conv_1x3;
conv_1x3 --> |"224*8*8"| conv_3x1;
conv_3x1 --> |"256*8*8"| concat;
concat --> |"448*8*8"| concat_proj;
concat_proj --> |"2144*8*8"| sum;
input --> |"2144*8*8"| sum;
sum --> |"2144*8*8"| bn;
bn --> |"2144*8*8"| output;
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
conv_3x3_a["Conv k=3 s=2 ReLU"];
conv_3x3_b_reduce["Conv k=1 ReLU"];
conv_3x3_b1["Conv k=3 p=1 ReLU"];
conv_3x3_b2["Conv k=3 s=2 ReLU"];
pool["MaxPool k=3 s=2"];
concat["DepthConcat"];
bn["BN ReLU"];
output["Output"];
input --> |"384*35*35"| conv_3x3_a;
input --> |"384*35*35"| conv_3x3_b_reduce;
input --> |"384*35*35"| pool;
pool --> |"384*17*17"| concat;
conv_3x3_a --> |"384*17*17"| concat;
conv_3x3_b_reduce --> |"256*35*35"| conv_3x3_b1;
conv_3x3_b1 --> |"256*35*35"| conv_3x3_b2;
conv_3x3_b2 --> |"384*17*17"| concat;
concat --> |"1152*17*17"| bn;
bn --> |"1152*17*17"| output;
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
conv_3x3_a_reduce["Conv k=1 ReLU"]
conv_3x3_a["Conv k=3 s=2 ReLU"];
conv_3x3_b_reduce["Conv k=1 ReLU"];
conv_3x3_b1["Conv k=3 p=1 ReLU"];
conv_3x3_b2["Conv k=3 s=2 ReLU"];
conv_3x3_c_reduce["Conv k=1 ReLU"];
conv_3x3_c["Conv k=3 s=2 ReLU"];
pool["MaxPool k=3 s=2"];
concat["DepthConcat"];
bn["BN ReLU"];
output["Output"];
input --> |"1152*17*17"| conv_3x3_a_reduce;
conv_3x3_a_reduce --> |"256*17*17"| conv_3x3_a;
conv_3x3_a --> |"384*8*8"| concat;
input --> |"1152*17*17"| conv_3x3_b_reduce;
conv_3x3_b_reduce --> |"256*17*17"| conv_3x3_b1;
conv_3x3_b1 --> |"288*17*17"| conv_3x3_b2;
conv_3x3_b2 --> |"320*8*8"| concat;
input --> |"1152*17*17"| conv_3x3_c_reduce;
conv_3x3_c_reduce --> |"256*17*17"| conv_3x3_c;
conv_3x3_c --> |"288*8*8"| concat;
input --> |"1152*17*17"| pool;
pool --> |"1152*8*8"| concat;
concat --> |"2144*8*8"| bn;
bn --> |"2144*8*8"| output;
</div>
</center>

### 实验结果

- ILSVRC 2012 上 144 切割的实验结果

|Network|Crops|Top-1 Error |Top-5 Error|
|:-:|:-:|:-:|:-:|
|ResNet-151|dense|19.4%|4.5%|
|Inception-v3|144|18.9%|4.3%|
|Inception-v4|144|17.7%|3.8%|
|Inception-ResNet-v1|144|18.8%|4.3%|
|Inception-ResNet-v2|144|17.8%|3.7%|