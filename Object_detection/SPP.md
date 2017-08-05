# SPP-net

R-CNN 的改进思路，Fast R-CNN借鉴其思路。比 R-CNN 快很多。

- 解决 CNN 需要固定大小输入的问题：在卷积层和全连接层之间加入特征集成的操作，以避免在输入图片时进行切割、变形等操作。
- 引入 Spatial Pyramid Pooling 层， 改变网络结构：
  - image --> **crop/warp** --> conv layers --> fc layers --> output
  - image --> conv layers --> **spatial pyramid layers** --> fc layers -->output

### 1. 问题

- 卷积层不需要限制图片大小，真正限制固定大小输入的是全连接层
- 图片经过卷积层后得到特征图（feature map）

### 2. SPP 层

- 为了让卷积神经网络应用到任意大小的图片上，把最后一个卷积-pooling 层的 pooling 层改成了 SPP 层。
- 最后一个卷积层输出的特征图被划分成不同尺度的小区域（如：4\*4， 2\*2，1\*1 ），在每个小区域内部做 pooling 生成对应长度的特征，把所有特征拼接在一起作为全连接层的输入：假设最后一个卷积层有 256 个滤波器，每个特征图被划分为 4\*4, 2\*2, 1\*1的小区域，那么生成的特征长度为 16\*256, 4\*256, 1\*256，拼接之后得到的21\*256维向量作为图片特征送入全连接层。
- SPP 层对图片的形变、空间位置的变化都具有鲁棒性。

### 3. 如何训练？

- 固定图片大小：假设最后一层输出大小为 $a \times a$ ， 区域个数为 $n \times n$ ，则取窗口大小为 $\lceil a/n \rceil$ ，跨距为 $\lfloor a/n \rfloor$ 。
- 不固定图片大小：图片大小变化意味着 $a$ 会改变，此时保持 $n$ 不变，按照以上方法重新设计 SPP 层，可以保证在图片尺寸改变时 SPP 层的输出维度不变。只改 SPP 层意味着网络的参数没有任何修改，因此可以通过保留参数切换网络结构的方法实现不同尺寸图片的统一训练。

### 4. 图片分类

- ImageNet 2012 top 1 error (%): 

|                 | ZF-5  | Convnet-5 | Overleaf-5 | Overfear-7 |
| :-------------: | :---: | :-------: | :--------: | :--------: |
|     no SPP      | 35.99 |   34.93   |   34.13    |    32.0    |
| SPP single-size | 34.98 |   34.38   |   32.87    |   30.36    |
| SPP multi-size  | 34.60 |   33.94   |   32.26    |   29.68    |

- 加入 SPP 提高分类性能：对图片的形变、空间位置的变化都具有鲁棒性。
- 加入 multi-size 提高分类性能：对图片分辨率的变化具有鲁棒性。

### 5. 物体检测

- 从整张图片提取特征图，对候选框应用 SPP。（*图片候选框如何映射到特征图？*）
- R-CNN 对每个候选框都要做一次卷积，包含大量冗余计算。SPP 可以避免这个问题
- 提取候选框用 Selective Search, 每张图大概提取2000个候选框
- 用 4 层的 spatial pyramid 来混合特征（1\*1, 2\*2, 3\*3, 6\*6）

### 6. 候选框到特征图的映射

1. 感受野的计算：output field size  = (input field size + 2 * padding - kernel size) / stride + 1,  ==> input field size = (output field size -1) * stride + kernel size - 2 * padding
2. 感受野上的坐标映射：设第 $i+1$ 层特征图上的某个点坐标为 $p_{i+1}$ ，则它映射到第 $i$ 层的点 $p_i$ （$p_{i+1}$ 对应到第 $i$ 层的感受野的中心点）的计算公式为：$p_i = p_{i+1} \times stride_i + ((kernel_i - 1)/2 - padding)$ 。如果取 $padding_i = \lfloor kernel_i/2 \rfloor$ ， 则上述公式可简化为 $p_i = p_{i+1} \times stride_i$ 。
3. 更加一般的结论：$p_0 = (\prod_{p=1}^Lstride_p) (p_L - 1) + 1 + \sum_{p = 1}^L(\prod_{q=1}^{p-1}stride_q)((kernel_p - 1) / 2 - padding_p)$ 。
4. SPP 作者的做法：采用 2 中的简化公式，令 $S = \prod_{i = 1}^L stride_i$，则在特征图上点 $(x', y')$ 映射到原图中坐标为 $(x, y) = (Sx', Sy')$ 。因此只需要把原图像中候选框的左上和右下两个角点映射到特征图中就可以唯一确定特征图中的候选框：左上角 $x' = \lfloor x/S \rfloor +1, \ y' = \lfloor y/S \rfloor +1$ ，右下角 $x' = \lceil x/S \rceil -1, \ y' = \lceil y/S \rceil -1$ 。




###### 参考文献 

###### 1. K. He, X. Zhang, S. Ren, and J. Sun. Spatial pyramid pooling in deep convolutional networks for visual recognition. In ECCV, 2014. 