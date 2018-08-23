# Inception v2

- 加入 Batch Normalization，减少 Internal Covariate Shift。
- 用两个连续的 $3 \times 3$ 卷积代替 v1 中的 $5 \times 5$ 卷积，既降低参数量，又加速计算

### Inception v2 结构

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
        document.getElementById('inception-v2-graph').style.display = "block";
        document.getElementById('show-button-inception-v2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('inception-v2-graph').style.display = "none";
        document.getElementById('show-button-inception-v2').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-inception-v2" onclick="ClickShowButton()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="inception-v2-graph" style="display: block">
    graph TD;
    image["image"];
    conv1["Conv k=7 s=2 p=3"];
    conv1_increase["Conv k=1 ReLU"]
    pool1["MaxPool k=3 s=2"];
    conv2_reduce["Conv k=1 BN ReLU"];
    conv2["Conv k=3 p=1 + BN ReLU"];
    pool2["MaxPool k=3 s=2"];
    inception_a1_1x1["Conv k=1 BN ReLU"];
    inception_a1_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_a1_3x3_a["Conv k=3 p=1 BN ReLU"];
    inception_a1_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_a1_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_a1_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_a1_pool["AvgPool k=3 p=1"];
    inception_a1_pool_proj["Conv k=1 BN ReLU"];
    inception_a1_output["DepthConcat"];
    inception_a2_1x1["Conv k=1 BN ReLU"];
    inception_a2_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_a2_3x3_a["Conv k=3 p=1 BN ReLU"];
    inception_a2_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_a2_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_a2_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_a2_pool["AvgPool k=3 p=1"];
    inception_a2_pool_proj["Conv k=1 BN ReLU"];
    inception_a2_output["DepthConcat"];
    reduction_a_3x3_a_reduce["Conv k=1 BN ReLU"];
    reduction_a_3x3_a["Conv k=3 s=2 p=1 BN ReLU"];
    reduction_a_3x3_b_reduce["Conv k=1 BN ReLU"];
    reduction_a_3x3_b1["Conv k=3 p=1 BN ReLU"];
    reduction_a_3x3_b2["Conv k=3 s=2 p=1 BN ReLU"];
    reduction_a_pool["MaxPool k=3 s=2 p=1"];
    reduction_a_output["DepthConcat"];
    inception_b1_1x1["Conv k=1 BN ReLU"];
    inception_b1_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_b1_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_b1_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_b1_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_b1_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_b1_pool["AvgPool k=3 p=1"];
    inception_b1_pool_proj["Conv k=1 BN ReLU"];
    inception_b1_output["DepthConcat"];
    inception_b2_1x1["Conv k=1 BN ReLU"];
    inception_b2_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_b2_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_b2_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_b2_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_b2_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_b2_pool["AvgPool k=3 p=1"];
    inception_b2_pool_proj["Conv k=1 BN ReLU"];
    inception_b2_output["DepthConcat"];
    inception_b3_1x1["Conv k=1 BN ReLU"];
    inception_b3_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_b3_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_b3_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_b3_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_b3_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_b3_pool["AvgPool k=3 p=1"];
    inception_b3_pool_proj["Conv k=1 BN ReLU"];
    inception_b3_output["DepthConcat"];
    inception_b4_1x1["Conv k=1 BN ReLU"];
    inception_b4_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_b4_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_b4_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_b4_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_b4_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_b4_pool["AvgPool k=3 p=1"];
    inception_b4_pool_proj["Conv k=1 BN ReLU"];
    inception_b4_output["DepthConcat"];
    reduction_b_3x3_a_reduce["Conv k=1 BN ReLU"];
    reduction_b_3x3_a["Conv k=3 s=2 p=1 BN ReLU"];
    reduction_b_3x3_b_reduce["Conv k=1 BN ReLU"];
    reduction_b_3x3_b1["Conv k=3 p=1 BN ReLU"];
    reduction_b_3x3_b2["Conv k=3 s=2 p=1 BN ReLU"];
    reduction_b_pool["MaxPool k=3 s=2 p=1"];
    reduction_b_output["DepthConcat"];
    inception_c1_1x1["Conv k=1 BN ReLU"];
    inception_c1_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_c1_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_c1_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_c1_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_c1_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_c1_pool["AvgPool k=3 p=1"];
    inception_c1_pool_proj["Conv k=1 BN ReLU"];
    inception_c1_output["DepthConcat"];
    inception_c2_1x1["Conv k=1 BN ReLU"];
    inception_c2_3x3_a_reduce["Conv k=1 BN ReLU"];
    inception_c2_3x3_a["Conv k=3 s=1 BN ReLU"];
    inception_c2_3x3_b_reduce["Conv k=1 BN ReLU"];
    inception_c2_3x3_b1["Conv k=3 p=1 BN ReLU"];
    inception_c2_3x3_b2["Conv k=3 p=1 BN ReLU"];
    inception_c2_pool["AvgPool k=3 p=1"];
    inception_c2_pool_proj["Conv k=1 BN ReLU"];
    inception_c2_output["DepthConcat"];
    pool3["AvgPool k=8 dp=0.2"];
    classifier["softmax classification"];
    image --> |"3*231*231"| conv1;
    conv1 --> |"24*116*116"| conv1_increase;
    conv1_increase --> |"64*116*116"| pool1;
    pool1 --> |"64*58*58"| conv2_reduce;
    conv2_reduce --> |"64*58*58"| conv2;
    conv2 --> |"192*58*58"| pool2;
    pool2 --> |"192*29*29"| inception_a1_1x1;
    pool2 --> |"192*29*29"| inception_a1_3x3_a_reduce;
    pool2 --> |"192*29*29"| inception_a1_3x3_b_reduce;
    pool2 --> |"192*29*29"| inception_a1_pool;
    inception_a1_3x3_a_reduce --> |"64*29*29"| inception_a1_3x3_a;
    inception_a1_3x3_b_reduce --> |"64*29*29"| inception_a1_3x3_b1;
    inception_a1_3x3_b1 --> |"96*29*29"| inception_a1_3x3_b2;
    inception_a1_pool --> |"192*29*29"| inception_a1_pool_proj;
    inception_a1_1x1 --> |"64*29*29"| inception_a1_output;
    inception_a1_3x3_a --> |"64*29*29"| inception_a1_output;
    inception_a1_3x3_b2 --> |"96*29*29"| inception_a1_output;
    inception_a1_pool_proj --> |"32*29*29"| inception_a1_output;
    inception_a1_output --> |"256*29*29"| inception_a2_1x1;
    inception_a1_output --> |"256*29*29"| inception_a2_3x3_a_reduce;
    inception_a1_output --> |"256*29*29"| inception_a2_3x3_b_reduce;
    inception_a1_output --> |"256*29*29"| inception_a2_pool;
    inception_a2_3x3_a_reduce --> |"64*29*29"| inception_a2_3x3_a;
    inception_a2_3x3_b_reduce --> |"64*29*29"| inception_a2_3x3_b1;
    inception_a2_3x3_b1 --> |"96*29*29"| inception_a2_3x3_b2;
    inception_a2_pool --> |"192*29*29"| inception_a2_pool_proj;
    inception_a2_1x1 --> |"64*29*29"| inception_a2_output;
    inception_a2_3x3_a --> |"64*29*29"| inception_a2_output;
    inception_a2_3x3_b2 --> |"96*29*29"| inception_a2_output;
    inception_a2_pool_proj --> |"32*29*29"| inception_a2_output;
    inception_a2_output --> |"320*29*29"| reduction_a_3x3_a_reduce;
    inception_a2_output --> |"320*29*29"| reduction_a_3x3_b_reduce;
    inception_a2_output --> |"320*29*29"| reduction_a_pool;
    reduction_a_3x3_a_reduce --> |"128*29*29"| reduction_a_3x3_a;
    reduction_a_3x3_b_reduce --> |"64*29*29"| reduction_a_3x3_b1;
    reduction_a_3x3_b1 --> |"96*29*29"| reduction_a_3x3_b2;
    reduction_a_3x3_a --> |"160*15*15"| reduction_a_output;
    reduction_a_3x3_b2 --> |"96*15*15"| reduction_a_output;
    reduction_a_pool --> |"320*15*15"| reduction_a_output;
    reduction_a_output --> |"576*15*15"| inception_b1_1x1;
    reduction_a_output --> |"576*15*15"| inception_b1_3x3_a_reduce;
    reduction_a_output --> |"576*15*15"| inception_b1_3x3_b_reduce;
    reduction_a_output --> |"576*15*15"| inception_b1_pool;
    inception_b1_3x3_a_reduce --> |"64*15*15"| inception_b1_3x3_a;
    inception_b1_3x3_b_reduce --> |"96*15*15"| inception_b1_3x3_b1;
    inception_b1_3x3_b1 --> |"128*15*15"| inception_b1_3x3_b2;
    inception_b1_pool --> |"576*15*15"| inception_b1_pool_proj;
    inception_b1_1x1 --> |"224*15*15"| inception_b1_output;
    inception_b1_3x3_a --> |"96*15*15"| inception_b1_output;
    inception_b1_3x3_b2 --> |"128*15*15"| inception_b1_output;
    inception_b1_pool_proj --> |"128*15*15"| inception_b1_output;
    inception_b1_output --> |"576*15*15"| inception_b2_1x1;
    inception_b1_output --> |"576*15*15"| inception_b2_3x3_a_reduce;
    inception_b1_output --> |"576*15*15"| inception_b2_3x3_b_reduce;
    inception_b1_output --> |"576*15*15"| inception_b2_pool;
    inception_b2_3x3_a_reduce --> |"96*15*15"| inception_b2_3x3_a;
    inception_b2_3x3_b_reduce --> |"96*15*15"| inception_b2_3x3_b1;
    inception_b2_3x3_b1 --> |"128*15*15"| inception_b2_3x3_b2;
    inception_b2_pool --> |"576*15*15"| inception_b2_pool_proj;
    inception_b2_1x1 --> |"192*15*15"| inception_b2_output;
    inception_b2_3x3_a --> |"128*15*15"| inception_b2_output;
    inception_b2_3x3_b2 --> |"128*15*15"| inception_b2_output;
    inception_b2_pool_proj --> |"128*15*15"| inception_b2_output;
    inception_b2_output --> |"576*15*15"| inception_b3_1x1;
    inception_b2_output --> |"576*15*15"| inception_b3_3x3_a_reduce;
    inception_b2_output --> |"576*15*15"| inception_b3_3x3_b_reduce;
    inception_b2_output --> |"576*15*15"| inception_b3_pool;
    inception_b3_3x3_a_reduce --> |"128*15*15"| inception_b3_3x3_a;
    inception_b3_3x3_b_reduce --> |"128*15*15"| inception_b3_3x3_b1;
    inception_b3_3x3_b1 --> |"160*15*15"| inception_b3_3x3_b2;
    inception_b3_pool --> |"576*15*15"| inception_b3_pool_proj;
    inception_b3_1x1 --> |"160*15*15"| inception_b3_output;
    inception_b3_3x3_a --> |"160*15*15"| inception_b3_output;
    inception_b3_3x3_b2 --> |"160*15*15"| inception_b3_output;
    inception_b3_pool_proj --> |"96*15*15"| inception_b3_output;
    inception_b3_output --> |"576*15*15"| inception_b4_1x1;
    inception_b3_output --> |"576*15*15"| inception_b4_3x3_a_reduce;
    inception_b3_output --> |"576*15*15"| inception_b4_3x3_b_reduce;
    inception_b3_output --> |"576*15*15"| inception_b4_pool;
    inception_b4_3x3_a_reduce --> |"128*15*15"| inception_b4_3x3_a;
    inception_b4_3x3_b_reduce --> |"160*15*15"| inception_b4_3x3_b1;
    inception_b4_3x3_b1 --> |"192*15*15"| inception_b4_3x3_b2;
    inception_b4_pool --> |"576*15*15"| inception_b4_pool_proj;
    inception_b4_1x1 --> |"192*15*15"| inception_b4_output;
    inception_b4_3x3_a --> |"192*15*15"| inception_b4_output;
    inception_b4_3x3_b2 --> |"192*15*15"| inception_b4_output;
    inception_b4_pool_proj --> |"96*15*15"| inception_b4_output;
    inception_b4_output --> |"576*15*15"| reduction_b_3x3_a_reduce;
    inception_b4_output --> |"576*15*15"| reduction_b_3x3_b_reduce;
    inception_b4_output --> |"576*15*15"| reduction_b_pool;
    reduction_b_3x3_a_reduce --> |"128*15*15"| reduction_b_3x3_a;
    reduction_b_3x3_b_reduce --> |"192*15*15"| reduction_b_3x3_b1;
    reduction_b_3x3_b1 --> |"256*15*15"| reduction_b_3x3_b2;
    reduction_b_3x3_a --> |"192*8*8"| reduction_b_output;
    reduction_b_3x3_b2 --> |"256*8*8"| reduction_b_output;
    reduction_b_pool --> |"576*8*8"| reduction_b_output;
    reduction_b_output --> |"1024*8*8"| inception_c1_1x1;
    reduction_b_output --> |"1024*8*8"| inception_c1_3x3_a_reduce;
    reduction_b_output --> |"1024*8*8"| inception_c1_3x3_b_reduce;
    reduction_b_output --> |"1024*8*8"| inception_c1_pool;
    inception_c1_3x3_a_reduce --> |"192*8*8"| inception_c1_3x3_a;
    inception_c1_3x3_b_reduce --> |"160*8*8"| inception_c1_3x3_b1;
    inception_c1_3x3_b1 --> |"224*8*8"| inception_c1_3x3_b2;
    inception_c1_pool --> |"1024*8*8"| inception_c1_pool_proj;
    inception_c1_1x1 --> |"352*8*8"| inception_c1_output;
    inception_c1_3x3_a --> |"320*8*8"| inception_c1_output;
    inception_c1_3x3_b2 --> |"224*8*8"| inception_c1_output;
    inception_c1_pool_proj --> |"128*8*8"| inception_c1_output;
    inception_c1_output --> |"1024*8*8"| inception_c2_1x1;
    inception_c1_output --> |"1024*8*8"| inception_c2_3x3_a_reduce;
    inception_c1_output --> |"1024*8*8"| inception_c2_3x3_b_reduce;
    inception_c1_output --> |"1024*8*8"| inception_c2_pool;
    inception_c2_3x3_a_reduce --> |"192*8*8"| inception_c2_3x3_a;
    inception_c2_3x3_b_reduce --> |"192*8*8"| inception_c2_3x3_b1;
    inception_c2_3x3_b1 --> |"224*8*8"| inception_c2_3x3_b2;
    inception_c2_pool --> |"1024*8*8"| inception_c2_pool_proj;
    inception_c2_1x1 --> |"352*8*8"| inception_c2_output;
    inception_c2_3x3_a --> |"320*8*8"| inception_c2_output;
    inception_c2_3x3_b2 --> |"224*8*8"| inception_c2_output;
    inception_c2_pool_proj --> |"128*8*8"| inception_c2_output;
    inception_c2_output --> |"1024*8*8"| pool3;
    pool3 --> |"1024"| classifier;
</div>
</center>

### 实验结果

|Model|Resolution|Crops|Models|Top-1 error|Top-5 error|
|:-:|:-:|:-:|:-:|:-:|:-:|
|GoogLeNet ensemble|224|144|7|-|6.67%|
|BN-Inception single crop|224|1|1|25.2%|7.82%|
|BN-Inception multicrop|224|144|1|21.99%|5.82%|
|**BN-Inception ensemble**|**224**|**144**|**6**|**20.1%**|**4.9%**|

### 参考文献

1. S. Ioffe and C. Szegedy. Batch normalization: Accelerating deep network training by reducing internal covariate shift. In Proceedings of The 32nd International Conference on Machine Learning, pages 448–456, 2015.