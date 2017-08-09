# R-CNN

物体检测的深度网络方法。

- Selective Search 生成候选框
- AlexNet 提取特征（用 VGG-16 提特征效果更好，但是复杂度太高）
- SVM 分类
- NMS 筛选窗口并输出

### 预处理

- Selective Search 提取候选框
- 候选框四个方向补16像素后 resize 为 227*227 大小（AlexNet 要求的输入大小）

### 训练

- AlexNet 预训练：ILSVRC2012 分类数据库，只有图片标注，没有 bounding box 。
- AlexNet 微调：用**所有**预选框（从所有图片的所有预选框里面采样得到 batch）训练 AlexNet，其中和真实物体区域的交并比（IoU）$>= 0.5$ 的候选框为正样本，其他为负样本。batch size 为128，32 个正样本窗口和 96 个背景窗口（一般在图片中背景窗口比较多所以背景窗口的数目较多）。如果待检测的物体有 N 类，则网络的分类输出为 N+1（N类 + 背景）。
- SVM 训练：用所有预选框训练二分类SVM。**SVM训练的交并比阈值和 AlexNet 不同，为0.3（通过Grid Search 得到）。** 对每一个类别分别训练一个线性SVM。


### 实验结果

- VOC 2010

|  Method  |   aero   |   bike   | ...  |   mAP    |
| :------: | :------: | :------: | :--: | :------: |
|   DPM    |   49.2   |   53.8   | ...  |   33.4   |
|  R-CNN   |   67.1   |   64.1   | ...  |   50.2   |
| R-CNN BB | **71.8** | **65.8** | ...  | **53.7** |



### 参考文献  

1. R. Girshick, J. Donahue, T. Darrell, and J. Malik. Rich feature hierarchies for accurate detection and semantic segmentation 



