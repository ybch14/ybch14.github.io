# Selective Search 

 物体检测中生成候选框（region proposal）的方法之一。通过区域初始化得到超像素，之后通过贪婪算法两两组合超像素直至区域大小达到全图；根据组合得到的区域，在不同尺度上得到候选框。

### 区域初始化

- 用图的方法表示图片，每个像素对应图中的一个节点v，每一条连接节点的无向边e具有一个权重，衡量连接的两个节点之间的不相似程度（权重越大越不相似）。
- 定义区域内间距：每个区域对应的最小生成树（MST）中最大权重。

$$\mathrm{Int}(C) =\max_{e \in MST(C, E)} \omega(e)$$

- 定义区域间间距：在所有属于两个区域且有连接的点中权重最小的一对（无连接的两个点的边权重无穷大）。

$$\mathrm{Dif}(C_1, C_2) = \min_{v_i\in C_1, v_j \in C_2, (v_i, v_j)\in E}\omega(v_i, v_j)$$

- 定义函数：$\mathrm{MInt}(C_1, C_2) = \min(\mathrm{Int}(C_1) + \tau(C_1), \mathrm{Int}(C_2) + \tau(C_2))$ ，$\tau$ 为阈值函数，用来控制两个区域间间距在多大程度上大于其区域内间距才能认为两个区域间有明显分割界限。
- 定义断言函数：

$$\mathrm{D}(C_1, C_2) = \begin{cases}\mathrm{true} & \mathrm{Dif}(C_1, C_2) > \mathrm{MInt}(C_1, C_2) \\ \mathrm{false} &\mathrm{else}\end{cases}$$

- 分割算法：输入为n个节点、m条边的图G，输出为一系列区域C
  - 所有的边按照权重以非递减方式排序
  - 令初始分割集合S(0)为每个点都属于一个区域
  - 由S(q-1)构造S(q)：记第q条边连接的节点为$v_i, v_j$ ，如果S(q-1)中这两个点属于两个区域，且其权重小于这两个区域的区域间间距，则合并两个区域，否则S(q) = S(q-1)。
  - q=1 到 q=m循环
  - 返回S(m) 为所求的分割区域集合
- 图的构造方法
  - Grid Graphs (GG) : 图像中每个元素视为一个节点，如果两个像素相邻则视为有连接，权重为两个节点像素差值的绝对值。
  - Nearest Neighbor Graphs (NNG) : 先把图像中每个像素映射到特征空间形成图的节点，然后利用特征空间中的距离度量，选择最近邻的点作为邻居，权重为两个节点在特征空间里的距离。
- NNG 方法能够更好的提取感知上的重要区域。

### 分层分组算法

- 用区域初始化的方法得到 $R = \{r_1, \dots , r_n\}$ 
- 构建相似集合S， 并初始化为空集
- 对R中的每一个相邻区域对 $(r_i, r_j)$ 计算相似性 $s(r_i, r_j)$ ，并将计算结果加入 S。
- 当S不为空集时，循环：
  - 取 $\max(S) = s(r_i, r_j)$ 
  - 合并区域 $r_t = r_i \cup r_j$  // 贪心算法合并区域
  - 从S中移除和 $r_i, r_j$ 有关的数据：$S = S\setminus s(r_i, :);\  S = S\setminus s(:, r_j)$ 。
  - 重新计算 $r_t$ 和与它相邻的区域的相似性，并将计算结果加入 S。
  - $R = R \cup r_t$ // 将不同尺度的区域都加入候选集合
- 从 R 中的每个元素提取候选框

### 相似性准则 $s(r_i, r_j)$

- 颜色 $s_{color}(r_i, r_j)$ ：对每个区域 $r_i$ 可以得到一个一维的颜色分布直方图 $C_i = \{c_i^1, c_i^2, \dots, c_i^n\}$ ，则$s_{color}(r_i, r_j) = \sum_{k = 1}^n \min(c_i^k, c_j^k)$ 。如果 i 和 j 合并为 t ，则合并区域的大小为$size(r_t) = size(r_i) +size(r_j)$， 直方图为 $C_t = \frac{size(r_i) \times C_i + size(r_j) \times C_j}{size(r_i) + size(r_j)}$ 。
- 纹理 $s_{texture}(r_i, r_j)$ ：取8个方向，方差为1的高斯滤波器，10个空间的直方图 $T_i = \{t_i^1, \dots, t_i^n\}$ ，则 $s_{texture}(r_i, r_j) = \sum_{k = 1}^n \min(t_i^k, t_j^k)$，合并区域的大小和直方图与颜色特征的计算方法相同。
- 尺寸 $s_{size}(r_i, r_j)$ ：$s_{size}(r_i, r_j) = 1- \frac{size(r_i) + size(r_j)}{size(image)}$ （鼓励小的区域尽早合并）。
- 包含 $s_{fill}(r_i, r_j)$ ：$s_{fill}(r_i, r_j) = 1- \frac{size(BB_{ij}) - size(r_i) - size(r_j)}{size(image)}$ ，$BB_{ij}$ 是包含 i 和 j 的最小外包区域（鼓励有相交或者有包含关系的区域尽早合并）。
- 通常使用的相似性度量：$s(r_i, r_j) = a_1 s_{color}(r_i, r_j) + a_2s_{texture}(r_i, r_j) + a_3 s_{size}(r_i, r_j) + a_4 s_{fill}(r_i, r_j)$



### 参考文献

1. J.Uijlings, K. van de Sande, T.Gevers, and A. Smeulders. Selective search for object recognition. IJCV, 2013 
2. Pedro F. Felzenszwalb, Daniel P. Huttenlocher. Efficient Graph-Based Image Segmentation. IJCV, 59:167–181, 2004. 