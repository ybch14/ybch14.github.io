# Recommender System Survey

论文链接：[http://de.arxiv.org/pdf/1707.07435v5](http://de.arxiv.org/pdf/1707.07435v5)

## Background

### Recommender System

推荐任务主要分3种：打分预测（Rating prediction）、排名预测（Ranking Prediction）和分类。
- Rating prediction：填补用户-产品打分矩阵的空白
- Ranking prediction：向用户推荐商品列表
- 分类：把候选商品按推荐的类别分类

推荐系统主要分3种：协同过滤（collaborative filtering），基于内容（content-based）的推荐系统以及混合推荐系统。分类的主要根据是输入数据的不同类别。
这些方法在处理稀疏数据、冷启动以及平衡不同评价指标这些问题上有各自的限制。
- 协同过滤：学习用户和产品之间的历史交集（显式：用户的评分、隐式：浏览历史）
- 基于内容的推荐系统：比较产品和用户的辅助信息（文本、图片、视频等）
- 混合推荐系统：把上面两种混在一起

## 两个分类维度

- 简单模型（只用一种网络结构）v.s. 组合模型（用多种网络结构）。
- 集成深度学习模型与传统模型 v.s. 只用深度学习模型

## Recent Works

- [Restricted Boltzmann Machines for collaborative filtering](https://www.cs.toronto.edu/~rsalakhu/papers/rbmcf.pdf)
- [Deep content-based music recommendation](https://papers.nips.cc/paper/5004-deep-content-based-music-recommendation.pdf)
- [Collaborative deep learning for recommender systems](https://arxiv.org/pdf/1409.2944.pdf)
- [Image-based recommendations on styles and substitutes](https://arxiv.org/pdf/1506.04757.pdf)
- [Deep neural networks for youtube recommendations](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/45530.pdf)
- [Session-based recommendations with recurrent neural networks](https://arxiv.org/pdf/1511.06939.pdf)
- [A multi-view deep learning approach for cross domain user modeling in recommendation systems](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/frp1159-songA.pdf)
- [Collaborative denoising auto-encoders for top-n recommender systems](http://alicezheng.org/papers/wsdm16-cdae.pdf)
- [Improving content-based and hybrid music recommendation using deep learning](https://www.smcnus.org/wp-content/uploads/2013/09/deep_mr.pdf)
- [Autorec: Autoencoders meet collaborative filtering](http://users.cecs.anu.edu.au/~u5098633/papers/www15.pdf)
- [Wide & deep learning for recommender systems](https://arxiv.org/pdf/1606.07792.pdf)
- [Visual Bayesian Personalized Ranking from Implicit Feedback](https://cseweb.ucsd.edu/~jmcauley/pdfs/aaai16.pdf)

## DL-based-RS

### 基于 MLP

### 单纯使用 MLP

- [Neural Collaborative Filtering (NCF)](https://www.comp.nus.edu.sg/~xiangnan/papers/ncf.pdf). 在多数情景下，推荐系统被看作是用户偏好和产品特征的相互作用，因此可以用一对神经网络刻画这种相互作用。NCF 就是这样的想法。 将用户ID和产品ID embedding之后经过一个多层感知机得到预测的二值分数（即 implicit feedback）。由于未被观察到的实例有很多（数据中只包含少量用户反馈的喜欢的产品，没有反馈的产品有很多），因此采用负采样的方式进行训练（skip-gram）。传统的矩阵分解模型可以看作NCF的一个特例。
- [Cross-domain Content-boosted Collaborative Filtering neural Network (CCCFNet)](https://pdfs.semanticscholar.org/9967/5f860615cbe8c2df0b537473371ce3cb35ea.pdf). 同样是一对神经网络的结构。用户对应一个神经网络，包括协同过滤因子和用户画像表征。产品对应一个神经网络，包括协同过滤因子和产品特征表征。
- [Wide & Deep Learning](https://arxiv.org/pdf/1606.07792.pdf). 深度学习的部分就是一个多层感知机。宽度学习的部分就是一个单层感知机（也可以看作一个广义线性模型）。这两种技术的结合使得推荐系统能够同时获取记忆能力和泛化能力。记忆能力体现在宽度学习上，可以直接从历史数据中获取信息；泛化能力体现在深度学习上，可以提取更加概括性的特征。然而在应用这项技术的时候，为深度学习和宽度学习选择合适的特征是很重要的一步。也就是说，哪些特征需要记忆、哪些特征需要泛化需要由人来决定。这一步会影响最终的推荐系统的效果。[DeepFM](https://arxiv.org/pdf/1703.04247.pdf) 的提出就是为了减少选择特征的工作量。
- [DeepFM](https://arxiv.org/pdf/1703.04247.pdf). 这项工作把 Factorization Machine 和 MLP 结合在了一起。它用神经网络对高阶的特征相互作用进行建模，用 Factorization Machine 对低阶的相互作用进行建模。两种机制结合的方式和 Wide & Deep Learning 相同。相比于 Wide & Deep Learning，DeepFM 把宽度学习的部分用 Factorization Machine 代替。

### MLP + 传统

- 注意性协同过滤：[Chen等人](https://www.comp.nus.edu.sg/~xiangnan/papers/sigir17-AttentiveCF.pdf)在隐因子模型上引入了两层注意，包括产品级别的注意和辅助信息级别的注意。产品级别的注意用于选择最能表征用户喜好的部分产品，辅助信息级别的注意用于从各种多媒体辅助信息中选择最重要的部分。[Alashkar等人](https://www.aaai.org/ocs/index.php/AAAI/AAAI17/paper/download/14773/13865)针对化妆推荐提出了一个基于MLP的模型。这项工作用两个相同的MLP对标注样例和专家经验进行学习，两者的参数同时更新，更新目标是使得两个MLP的输出差别最小。这项工作证明在推荐系统中引入专家经验能帮助引导学习过程。[Covingtom等人](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/45530.pdf)在Youtube推荐系统中使用了MLP。这项工作把此产品推荐分成了候选商品生成和候选商品排序两个步骤。候选商品生成从所有Youtube视频中找到数百个候选视频，之后排序模块对这些视频进行排序，生成Top-N推荐列表。

## 基于 AutoEncoder (AE)

在推荐系统中使用 AutoEncoder 主要有两种思路：用 AE 学习低维的特征向量（AE的瓶颈层）；直接用 AE 的重建层填补 rating 矩阵中的空白。

### 单纯使用 AE

- [AutoRec](http://users.cecs.anu.edu.au/~u5098633/papers/www15.pdf): AutoRec 以用户和产品的部分观察向量（partially observed vector）为输入，目标是在输出层对它们进行重构。该模型有两个变种：基于用户的 AutoRec (U-AutoRec) 和基于产品的 AutoRec (I-AutoRec)。以 I-AutoRec 为例，经过 AE 之后的向量可以表示为 $h(\mathbf{\mathrm{r}}^{(i)}; \theta) = f(W\cdot g(V\cdot \mathbf{\mathrm{r}}^{(i)} + \mu) + b)$（$f, g$是激活函数，$\theta=\{W, V, \mu, b\}$）。I-AutoRec的优化目标就是让 $|\mathbf{\mathrm{r}}^{(i)}$ 和 $h(\mathbf{\mathrm{r}}^{(i)}; \theta)$ 尽可能地接近。在使用 AutoRec 的时候需要注意：（1）I-AutoRec 比 U-AutoRec 效果好，可能因为用户向量的变化性比较大；（2）AE 中两个层的激活函数的不同组合会明显影响性能；（3）适当增加隐藏层维度可以提升性能；（4）堆叠更多的层形成更深的网络也可以提升性能。
- [Collaborative Filtering Neural Network (CFN)](https://arxiv.org/pdf/1606.07659.pdf): AutoRec 的扩展。它采用噪声抑制技术，增强鲁棒性；它引入外部信息（用户画像、产品描述等）解决稀疏性和冷启动问题。CFN 的输入也是部分观察向量，因此也有两个变种：I-CFN 和 U-CFN。为了更好地处理输入向量中的遗失部分，作者在输入中加入了噪声干扰（作为较强的规范项）。更进一步，CFN 在 AE 的每一层中都加入了外部信息的辅助。外部信息的引入提高预测精度，加快训练进程，提高鲁棒性。
- [Autoencoder-based Collaborative Filtering (ACF)](https://link.springer.com/chapter/10.1007/978-3-319-12643-2_35): ACF 的输入不是原始的部分向量，是按照评分等级分解的部分向量（如果评分是1-5，就分解成5个）。其优化目标是重建向量与输入向量的 MSE 最小。训练的时候用 RBM 预训练。两个问题：（1）不能预测非整数评分；（2）输入向量的分解增加稀疏性，降低性能。
- [Collaborative Denoising Auto-Encoder (CDAE)](http://alicezheng.org/papers/wsdm16-cdae.pdf): 为 Ranking 任务设计的模型。CDAE的输入是用户的隐式反馈部分观察向量（partial observed implicit feedback），其中如果用户喜欢该产品，该产品对应的向量值为1，否则为0。输入向量也可以看作用户的偏好向量。另外，除了经过噪声干扰的偏好向量，AE 的输入还包括用户的权重矩阵。加入用户的权重之后性能有很大的提升。另外，由于现实应用中数据规模过大，因此在优化过程中使用负采样技术。

### AE + 传统

- [Collaborative Deep Learning (CDL)](https://arxiv.org/pdf/1409.2944.pdf): 把 SDAE (stacked denoising autoencoder) 集成到 probabilistic matrix factorization (PMF) 中。为了无缝融合两种技术，作者提出了一种通用贝叶斯深度学习框架，包括两个高度耦合的模块：感知机模块和任务专用模块。在 CDL 中 SDAE 充当感知机模块，PMF 充当任务专用模块。这样的组合使得 CDL 可以平衡侧面信息和评分信息的作用。CDL的一个扩展是 CVAE (collaborative variational autoencoder)，用一个 VAE 代替了 CDL 中的 SDAE。CVAE 可以从内容信息中学习隐变量，可以很容易地利用多媒体数据。
- [Collaborative Deep Ranking (CDL)](https://link.springer.com/chapter/10.1007/978-3-319-31750-2_44): 相比于为评分预测任务设计的 CDL，CDR 是专门给排序推荐任务设计的模型，采用的是一对对称的网络模型。
- [Deep Collaborative Filtering Framework](https://dl.acm.org/citation.cfm?id=2806527): 这是一个通用于协同过滤模型的深度学习方法。它将协同过滤任务建模为 $\operatorname*{argmin}_{U,V} l(R,U,V)+\beta(||U||_F^2+||V||_F^2)+\gamma L(X,U) + \delta L(Y, V)$。其中 $X,Y$ 是外部信息，$l(\cdot)$ 是协同过滤的损失函数，$L(X,U), L(Y, V)$ 是链接深度学习与协同模型、外部信息与隐式因子（latent factor）的桥梁。在此基础上，作者还提出了mDA-CF模型，相比 CDL 其计算效率和可扩展性都有提高。
- [AutoSVD++](https://arxiv.org/pdf/1704.00551.pdf): 这项工作使用收缩自动编码器学习产品的特征向量，并把这些特征集成到了传统的 SVD++ 模型中。优点：（1）收缩自动编码器能够获取极其微小的输入偏差；（2）对隐式反馈进行建模，提高性能；（3）作者针对这一模型设计了专有的优化算法，能够减少训练时间。
- [HRCD](https://ieeexplore.ieee.org/document/7588947): 这项工作是一个混合协同模型，主要基于 autoencoder 和 timeSVD++。这项工作利用到了时间信息，用 SDAE 学习产品特征，目的是解决冷启动问题。
