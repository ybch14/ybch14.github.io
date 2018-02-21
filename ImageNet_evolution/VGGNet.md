# VGG

网络层数进一步加深至 16-19 层，ILSVRC 2014 第二名。

### 中间层细节

网络输入是 $224 \times 224$ 的 RGB 图片。唯一的预处理是在图片上减去均值。卷积层的感受野都非常小：$3\times 3$。除此之外 $1\times 1$ 的卷积也可以用于对特征图的通道进行线性变换（之后进行非线性操作）。卷积跨距固定为 1，通过补零保证空间尺寸不变（$3\times 3$ 的卷积核补零为 1）。池化层跟在某些卷积层的后面，池化核大小为 $2\times 2$，跨距为 2。

在卷积层后面是 3 个全连接层；前两个有 4096 个通道，第三个是 ImageNet softmax 分类器，有 1000 个通道。

所有隐藏层的激活函数都是 ReLU。网络中没有采用 LRN 层是因为实验结果表明 LRN 对性能没有明显的提高。

### 网络结构

- VGG-16

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
        document.getElementById('vgg-16-graph').style.display = "block";
        document.getElementById('show-button-vgg-16').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('vgg-16-graph').style.display = "none";
        document.getElementById('show-button-vgg-16').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-vgg-16" onclick="ClickShowButton()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="vgg-16-graph" style="display: block">
graph TD;
image["Image"];
conv1_1["Conv k=3 p=1 ReLU"];
conv1_2["Conv k=3 p=1 ReLU"];
pool1["MaxPool k=2 s=2"];
conv2_1["Conv k=3 p=1 ReLU"];
conv2_2["Conv k=3 p=1 ReLU"];
pool2["MaxPool k=2 s=2"];
conv3_1["Conv k=3 p=1 ReLU"];
conv3_2["Conv k=3 p=1 ReLU"];
conv3_3["Conv k=3 p=1 ReLU"];
pool3["MaxPool k=2 s=2"];
conv4_1["Conv k=3 p=1 ReLU"];
conv4_2["Conv k=3 p=1 ReLU"];
conv4_3["Conv k=3 p=1 ReLU"];
pool4["MaxPool k=2 s=2"];
conv5_1["Conv k=3 p=1 ReLU"];
conv5_2["Conv k=3 p=1 ReLU"];
conv5_3["Conv k=3 p=1 ReLU"];
pool5["MaxPool k=2 s=2"];
fc6["4096 dp=0.5"];
fc7["4096 dp=0.5"];
pred["1000 softmax"];
image --> |"3*224*224"| conv1_1;
conv1_1 --> |"64*224*224"| conv1_2;
conv1_2 --> |"64*224*224"| pool1;
pool1 --> |"64*112*112"| conv2_1;
conv2_1 --> |"128*112*112"| conv2_2;
conv2_2 --> |"128*112*112"| pool2;
pool2 --> |"128*56*56"| conv3_1;
conv3_1 --> |"256*56*56"| conv3_2;
conv3_2 --> |"256*56*56"| conv3_3;
conv3_3 --> |"256*56*56"| pool3;
pool3 --> |"256*28*28"| conv4_1;
conv4_1 --> |"512*28*28"| conv4_2;
conv4_2 --> |"512*28*28"| conv4_3;
conv4_3 --> |"512*28*28"| pool4;
pool4 --> |"512*14*14"| conv5_1;
conv5_1 --> |"512*14*14"| conv5_2;
conv5_2 --> |"512*14*14"| conv5_3;
conv5_3 --> |"512*14*14"| pool5;
pool5 --> |"512*7*7"| fc6;
fc6 --> |"4096"| fc7;
fc7 --> |"4096"| pred;
</div>
</center>

- VGG-19

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
        document.getElementById('vgg-19-graph').style.display = "block";
        document.getElementById('show-button-vgg-19').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Hide Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = true;
    }
    else
    {
        document.getElementById('vgg-19-graph').style.display = "none";
        document.getElementById('show-button-vgg-19').innerHTML = "<span id=\"button-left\"><i class=\"demo-icon icon-sitemap\"></i> Show Network</span><span id=\"button-right\"><i class=\"demo-icon icon-down-open\"></i></span></button></center></center>";
        is_show = false;
    }
}
</script>
<center><button class="button show" id="show-button-vgg-19" onclick="ClickShowButton()">
<span id="button-left">
<i class="demo-icon icon-sitemap"></i> Hide Network
</span>
<span id="button-right">
<i class="demo-icon icon-down-open"></i>
</span></button></center>
<center>
<div class="mermaid" id="vgg-19-graph" style="display: block">
graph TD;
image["Image"];
conv1_1["Conv k=3 p=1 ReLU"];
conv1_2["Conv k=3 p=1 ReLU"];
pool1["MaxPool k=2 s=2"];
conv2_1["Conv k=3 p=1 ReLU"];
conv2_2["Conv k=3 p=1 ReLU"];
pool2["MaxPool k=2 s=2"];
conv3_1["Conv k=3 p=1 ReLU"];
conv3_2["Conv k=3 p=1 ReLU"];
conv3_3["Conv k=3 p=1 ReLU"];
conv3_4["Conv k=3 p=1 ReLU"];
pool3["MaxPool k=2 s=2"];
conv4_1["Conv k=3 p=1 ReLU"];
conv4_2["Conv k=3 p=1 ReLU"];
conv4_3["Conv k=3 p=1 ReLU"];
conv4_4["Conv k=3 p=1 ReLU"];
pool4["MaxPool k=2 s=2"];
conv5_1["Conv k=3 p=1 ReLU"];
conv5_2["Conv k=3 p=1 ReLU"];
conv5_3["Conv k=3 p=1 ReLU"];
conv5_4["Conv k=3 p=1 ReLU"];
pool5["MaxPool k=2 s=2"];
fc6["4096 dp=0.5"];
fc7["4096 dp=0.5"];
pred["1000 softmax"];
image --> |"3*224*224"| conv1_1;
conv1_1 --> |"64*224*224"| conv1_2;
conv1_2 --> |"64*224*224"| pool1;
pool1 --> |"64*112*112"| conv2_1;
conv2_1 --> |"128*112*112"| conv2_2;
conv2_2 --> |"128*112*112"| pool2;
pool2 --> |"128*56*56"| conv3_1;
conv3_1 --> |"256*56*56"| conv3_2;
conv3_2 --> |"256*56*56"| conv3_3;
conv3_3 --> |"256*56*56"| conv3_4;
conv3_4 --> |"256*56*56"| pool3;
pool3 --> |"256*28*28"| conv4_1;
conv4_1 --> |"512*28*28"| conv4_2;
conv4_2 --> |"512*28*28"| conv4_3;
conv4_3 --> |"512*28*28"| conv4_4;
conv4_4 --> |"512*28*28"| pool4;
pool4 --> |"512*14*14"| conv5_1;
conv5_1 --> |"512*14*14"| conv5_2;
conv5_2 --> |"512*14*14"| conv5_3;
conv5_3 --> |"512*14*14"| conv5_4;
conv5_4 --> |"512*14*14"| pool5;
pool5 --> |"512*7*7"| fc6;
fc6 --> |"4096"| fc7;
fc7 --> |"4096"| pred;
</div>
</center>

### 讨论

和 AlexNet 不同，VGG 没有采用大的卷积核，而是采用感受野更小的 $3\times 3$ 卷积核。容易看出，两个 $3\times 3$ 的卷积核和一个 $5\times 5$ 卷积核的感受野大小相同，3个 $3\times 3$ 的卷积核和一个 $7\times 7$ 的卷积核感受野大小相同。采用 $3\times 3$ 卷积代替大卷积核的原因有两个：一是可以增加网络的非线性，使得网络的判决函数具有更强的分辨力；二是减少网络参数。

### 网络训练

loss 函数为多项逻辑回归函数。Batch size 256，momentum 0.9，学习率 0.01。相比 AlexNet，VGG 收敛更快，是因为（a）网络的深度增加，卷积核尺寸很小，使得网络中引入了更多的规范项；（b）特定层经过预初始化。

网络参数的初始化非常重要，因为初始化不好的话会使得网络中梯度不稳定，阻塞学习过程。为了避免这种情况，作者先训练了一个浅层的网络（前两个阶段只有一个卷积层，后三个阶段只有两个卷积层），层数足够少，参数可以随机初始化。在训练更深的网络时，用浅层网络的参数初始化深层网络的前四个卷积层和后三个全连接层。对于浅层网络的参数，用零均值，$10^{-2}$ 方差的正态分布初始化。偏置初始化为0。

固定的 $224 \times 224$ 图片是从缩放的训练图片中随机切割得到（每张图片每次迭代切割一次）。为了进一步对训练集做增强，对切割出来的图片做随机水平翻转和颜色转换。对于图片缩放，有两种方法：一是固定短边的缩放尺寸（实验中采取 256 和 384 两种尺寸）；二是随机选择短边的缩放尺寸（实验中最小值取 256，最大值取 512）。处于速度考虑，先用固定缩放的方法（384）预训练，之后用随机缩放的方法对网络参数进行微调。

### 测试

测试时把测试图片缩放到一个固定尺寸（不一定和训练时的缩放尺寸相同）。将网络结尾的三个全连接层改为卷积层：第一个 fc 的输入为 $512\times 7\times 7$，输出为 4096，可以用一个 $7\times 7$，输出通道 4096 的卷积核代替；后面的两个全连接层同理，可以用 $1\times 1$，输出通道为 4096 / 1000 的全连接层代替。经过修改结构后网络变为全卷积网络，可以处理任意尺寸的图片；对于 $224\times 224$ 的图片，输出为 1000 长度的分类分数；对于大于 $224\times 224$ 的图片，如 $384\times 384$ 的图片，其输出为 $6\times 6\times 1000$，其输出结果等价于在输入图片上 36 个位置的 $224\times 224$ 切割图片使用原始的 CNN 得到分类分数，计算效率有巨大提高。为了得到统一长度的分类分数，输出的特征图在空间上进行池化（对每个通道求和），得到的向量即为 1000 类的分类向量。对于测试集，加入水平翻转进行增强；同一张图片的两个分数向量求平均得到一张图片最终的分数向量。

### 实验结果

|Method|Top-5 test error (%)|
|:-:|:-:|
|VGG (2 nets, multi-crop & dense eval.)|6.8|
|VGG (1 net, multi-crop & dense eval.)|7.0|
|VGG (ILSVRC submission, 7 nets, dense eval.)|7.3|
|GoogLeNet (1 net)|7.9|
|GoogLeNet (7 nets)|6.7|
|SPPNet (11 nets)|8.1|
|SPPNet (1 net)|9.1|
|Clarifai(multiple nets)|11.7|
|Clarifai(1 net)|12.5|
|ZFNet (6 nets)|14.8|
|ZFNet (1 net)|16.1|
|OverFeat (7 nets)|13.6|
|AlexNet (5 nets)|16.4|

### 参考文献

1. K. Simonyan and A. Zisserman. Very deep convolutional networks for large-scale image recognition. In ICLR, 2015.