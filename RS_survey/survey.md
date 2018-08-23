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

## 近年最受关注的工作

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

## 基于深度学习的推荐系统

### 基于 MLP

### 单纯使用 MLP

- [Neural Collaborative Filtering (NCF)](https://www.comp.nus.edu.sg/~xiangnan/papers/ncf.pdf). 在多数情景下，推荐系统被看作是用户偏好和产品特征的相互作用，因此可以用一对神经网络刻画这种相互作用。NCF 就是这样的想法。 将用户ID和产品ID embedding之后经过一个多层感知机得到预测的二值分数（即 implicit feedback）。由于未被观察到的实例有很多（数据中只包含少量用户反馈的喜欢的产品，没有反馈的产品有很多），因此采用负采样的方式进行训练（skip-gram）。传统的矩阵分解模型可以看作NCF的一个特例。
- [Cross-domain Content-boosted Collaborative Filtering neural Network (CCCFNet)](https://pdfs.semanticscholar.org/9967/5f860615cbe8c2df0b537473371ce3cb35ea.pdf). 同样是一对神经网络的结构。用户对应一个神经网络，包括协同过滤因子和用户画像表征。产品对应一个神经网络，包括协同过滤因子和产品特征表征。
- [Wide & Deep Learning](https://arxiv.org/pdf/1606.07792.pdf). 深度学习的部分就是一个多层感知机。宽度学习的部分就是一个单层感知机（也可以看作一个广义线性模型）。这两种技术的结合使得推荐系统能够同时获取记忆能力和泛化能力。记忆能力体现在宽度学习上，可以直接从历史数据中获取信息；泛化能力体现在深度学习上，可以提取更加概括性的特征。然而在应用这项技术的时候，为深度学习和宽度学习选择合适的特征是很重要的一步。也就是说，哪些特征需要记忆、哪些特征需要泛化需要由人来决定。这一步会影响最终的推荐系统的效果。[DeepFM](https://arxiv.org/pdf/1703.04247.pdf) 的提出就是为了减少选择特征的工作量。
- [DeepFM](https://arxiv.org/pdf/1703.04247.pdf). 这项工作把 Factorization Machine 和 MLP 结合在了一起。它用神经网络对高阶的特征相互作用进行建模，用 Factorization Machine 对低阶的相互作用进行建模。两种机制结合的方式和 Wide & Deep Learning 相同。相比于 Wide & Deep Learning，DeepFM 把宽度学习的部分用 Factorization Machine 代替。