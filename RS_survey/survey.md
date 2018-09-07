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

## 单DL模型推荐系统

### 基于 MLP

#### 单纯使用 MLP

- [Neural Collaborative Filtering (NCF)](https://www.comp.nus.edu.sg/~xiangnan/papers/ncf.pdf). 在多数情景下，推荐系统被看作是用户偏好和产品特征的相互作用，因此可以用一对神经网络刻画这种相互作用。NCF 就是这样的想法。 将用户ID和产品ID embedding之后经过一个多层感知机得到预测的二值分数（即 implicit feedback）。由于未被观察到的实例有很多（数据中只包含少量用户反馈的喜欢的产品，没有反馈的产品有很多），因此采用负采样的方式进行训练（skip-gram）。传统的矩阵分解模型可以看作NCF的一个特例。
- [Cross-domain Content-boosted Collaborative Filtering neural Network (CCCFNet)](https://pdfs.semanticscholar.org/9967/5f860615cbe8c2df0b537473371ce3cb35ea.pdf). 同样是一对神经网络的结构。用户对应一个神经网络，包括协同过滤因子和用户画像表征。产品对应一个神经网络，包括协同过滤因子和产品特征表征。
- [Wide & Deep Learning](https://arxiv.org/pdf/1606.07792.pdf). 深度学习的部分就是一个多层感知机。宽度学习的部分就是一个单层感知机（也可以看作一个广义线性模型）。这两种技术的结合使得推荐系统能够同时获取记忆能力和泛化能力。记忆能力体现在宽度学习上，可以直接从历史数据中获取信息；泛化能力体现在深度学习上，可以提取更加概括性的特征。然而在应用这项技术的时候，为深度学习和宽度学习选择合适的特征是很重要的一步。也就是说，哪些特征需要记忆、哪些特征需要泛化需要由人来决定。这一步会影响最终的推荐系统的效果。[DeepFM](https://arxiv.org/pdf/1703.04247.pdf) 的提出就是为了减少选择特征的工作量。
- [DeepFM](https://arxiv.org/pdf/1703.04247.pdf). 这项工作把 Factorization Machine 和 MLP 结合在了一起。它用神经网络对高阶的特征相互作用进行建模，用 Factorization Machine 对低阶的相互作用进行建模。两种机制结合的方式和 Wide & Deep Learning 相同。相比于 Wide & Deep Learning，DeepFM 把宽度学习的部分用 Factorization Machine 代替。

#### MLP + 传统

- 注意性协同过滤：[Chen等人](https://www.comp.nus.edu.sg/~xiangnan/papers/sigir17-AttentiveCF.pdf)在隐因子模型上引入了两层注意，包括产品级别的注意和辅助信息级别的注意。产品级别的注意用于选择最能表征用户喜好的部分产品，辅助信息级别的注意用于从各种多媒体辅助信息中选择最重要的部分。[Alashkar等人](https://www.aaai.org/ocs/index.php/AAAI/AAAI17/paper/download/14773/13865)针对化妆推荐提出了一个基于MLP的模型。这项工作用两个相同的MLP对标注样例和专家经验进行学习，两者的参数同时更新，更新目标是使得两个MLP的输出差别最小。这项工作证明在推荐系统中引入专家经验能帮助引导学习过程。[Covingtom等人](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/45530.pdf)在Youtube推荐系统中使用了MLP。这项工作把此产品推荐分成了候选商品生成和候选商品排序两个步骤。候选商品生成从所有Youtube视频中找到数百个候选视频，之后排序模块对这些视频进行排序，生成Top-N推荐列表。

### 基于 AutoEncoder (AE)

在推荐系统中使用 AutoEncoder 主要有两种思路：用 AE 学习低维的特征向量（AE的瓶颈层）；直接用 AE 的重建层填补 rating 矩阵中的空白。

#### 单纯使用 AE

- [AutoRec](http://users.cecs.anu.edu.au/~u5098633/papers/www15.pdf): AutoRec 以用户和产品的部分观察向量（partially observed vector）为输入，目标是在输出层对它们进行重构。该模型有两个变种：基于用户的 AutoRec (U-AutoRec) 和基于产品的 AutoRec (I-AutoRec)。以 I-AutoRec 为例，经过 AE 之后的向量可以表示为 $h(\mathbf{\mathrm{r}}^{(i)}; \theta) = f(W\cdot g(V\cdot \mathbf{\mathrm{r}}^{(i)} + \mu) + b)$（$f, g$是激活函数，$\theta=\\{W, V, \mu, b\\}$）。I-AutoRec的优化目标就是让 $\mathbf{\mathrm{r}}^{(i)}$ 和 $h(\mathbf{\mathrm{r}}^{(i)}; \theta)$ 尽可能地接近。在使用 AutoRec 的时候需要注意：（1）I-AutoRec 比 U-AutoRec 效果好，可能因为用户向量的变化性比较大；（2）AE 中两个层的激活函数的不同组合会明显影响性能；（3）适当增加隐藏层维度可以提升性能；（4）堆叠更多的层形成更深的网络也可以提升性能。
- [Collaborative Filtering Neural Network (CFN)](https://arxiv.org/pdf/1606.07659.pdf): AutoRec 的扩展。它采用噪声抑制技术，增强鲁棒性；它引入外部信息（用户画像、产品描述等）解决稀疏性和冷启动问题。CFN 的输入也是部分观察向量，因此也有两个变种：I-CFN 和 U-CFN。为了更好地处理输入向量中的遗失部分，作者在输入中加入了噪声干扰（作为较强的规范项）。更进一步，CFN 在 AE 的每一层中都加入了外部信息的辅助。外部信息的引入提高预测精度，加快训练进程，提高鲁棒性。
- [Autoencoder-based Collaborative Filtering (ACF)](https://link.springer.com/chapter/10.1007/978-3-319-12643-2_35): ACF 的输入不是原始的部分向量，是按照评分等级分解的部分向量（如果评分是1-5，就分解成5个）。其优化目标是重建向量与输入向量的 MSE 最小。训练的时候用 RBM 预训练。两个问题：（1）不能预测非整数评分；（2）输入向量的分解增加稀疏性，降低性能。
- [Collaborative Denoising Auto-Encoder (CDAE)](http://alicezheng.org/papers/wsdm16-cdae.pdf): 为 Ranking 任务设计的模型。CDAE的输入是用户的隐式反馈部分观察向量（partial observed implicit feedback），其中如果用户喜欢该产品，该产品对应的向量值为1，否则为0。输入向量也可以看作用户的偏好向量。另外，除了经过噪声干扰的偏好向量，AE 的输入还包括用户的权重矩阵。加入用户的权重之后性能有很大的提升。另外，由于现实应用中数据规模过大，因此在优化过程中使用负采样技术。

#### AE + 传统

- [Collaborative Deep Learning (CDL)](https://arxiv.org/pdf/1409.2944.pdf): 把 SDAE (stacked denoising autoencoder) 集成到 probabilistic matrix factorization (PMF) 中。为了无缝融合两种技术，作者提出了一种通用贝叶斯深度学习框架，包括两个高度耦合的模块：感知机模块和任务专用模块。在 CDL 中 SDAE 充当感知机模块，PMF 充当任务专用模块。这样的组合使得 CDL 可以平衡侧面信息和评分信息的作用。CDL的一个扩展是 CVAE (collaborative variational autoencoder)，用一个 VAE 代替了 CDL 中的 SDAE。CVAE 可以从内容信息中学习隐变量，可以很容易地利用多媒体数据。
- [Collaborative Deep Ranking (CDL)](https://link.springer.com/chapter/10.1007/978-3-319-31750-2_44): 相比于为评分预测任务设计的 CDL，CDR 是专门给排序推荐任务设计的模型，采用的是一对对称的网络模型。
- [Deep Collaborative Filtering Framework](https://dl.acm.org/citation.cfm?id=2806527): 这是一个通用于协同过滤模型的深度学习方法。它将协同过滤任务建模为 $\operatorname*{argmin}_{U,V} l(R,U,V)+\beta(\|\|U\|\|_F^2+\|\|V\|\|_F^2)+\gamma L(X,U) + \delta L(Y, V)$。其中 $X,Y$ 是外部信息，$l(\cdot)$ 是协同过滤的损失函数，$L(X,U), L(Y, V)$ 是链接深度学习与协同模型、外部信息与隐式因子（latent factor）的桥梁。在此基础上，作者还提出了mDA-CF模型，相比 CDL 其计算效率和可扩展性都有提高。
- [AutoSVD++](https://arxiv.org/pdf/1704.00551.pdf): 这项工作使用收缩自动编码器学习产品的特征向量，并把这些特征集成到了传统的 SVD++ 模型中。优点：（1）收缩自动编码器能够获取极其微小的输入偏差；（2）对隐式反馈进行建模，提高性能；（3）作者针对这一模型设计了专有的优化算法，能够减少训练时间。
- [HRCD](https://ieeexplore.ieee.org/document/7588947): 这项工作是一个混合协同模型，主要基于 autoencoder 和 timeSVD++。这项工作利用到了时间信息，用 SDAE 学习产品特征，目的是解决冷启动问题。

### 基于 CNN

#### 单纯基于 CNN

- [Attention based CNN](https://www.ijcai.org/Proceedings/16/Papers/395.pdf): 这项工作用基于注意的 CNN 做微博的 hashtag 推荐。任务本身可以建模成一个多分类问题。该模型有两个通道，全局通道局部注意通道。全局通道包括正常的卷积核和 max-pooling 层。所有词是全局通道的输入。局部注意通道有一个注意层，用来选择一个局部窗口中有信息的词，即出发词。在接下来的层中，只有触发词发挥作用。
- [Personalized CNN Tag Recommendation](https://link.springer.com/chapter/10.1007/978-3-319-57454-7_15): 这项工作用 CNN 获取图片中的信息。为了生成个性化推荐结果，用户信息被插入到 CNN 中。网络的优化采用 Bayesian Personalized Ranking 算法，最大化有关和无关 tag 之间的差别。

#### CNN + 传统

- [Deep Cooperative Neural Network (DeepCoNN)](https://arxiv.org/pdf/1701.04783.pdf): 这项工作用两个平行的 CNN 从评论文本中提取特征，对用户行为和产品属性进行建模。在最后一层中，用 FM (Factorization Machine) 获取用户和产品信息的相互联系，用于进行评分预测。该模型减轻了稀疏性的问题，通过挖掘评论文本中的语义信息增强了模型的可解释性。
- [ConvMF](http://uclab.khu.ac.kr/resources/publication/C_351.pdf): 融合了 CNN 和 PMF，融合方式与 CDL 相似。不同的是，CDL 用 AE 学习产品的特征，而 ConvMF 用 CNN 学习高层次的产品特征。ConvMF 的主要优势在于 CNN 可以提取更加精确的上下文信息。

### 基于 RNN

#### 单纯基于 RNN

- Session-based Recommendation with RNN: 在互联网上，会话和cookie机制使得推荐系统能够获取短时间内的用户偏好。[Hidasl 等人](https://arxiv.org/pdf/1511.06939.pdf)提出了一个基于会话的 GRU 推荐系统，输入是会话的 one-hot 向量，在会话中出现的产品对应的向量位为1。输出是会话中下一个产品的似然值。作者还提出了会话并行的批次训练方法以提高训练效率。[Wu 等人](https://ieeexplore.ieee.org/document/7498326)用普通 RNN 设计了一个基于会话的电商推荐系统，从用户的点击历史中预测下一个会购买的商品。为了减少计算开销，模型中只保留最新的有限个状态，更早的状态都折叠到一个单独的历史状态中。上面提到的两种推荐系统都没有考虑到外部信息。[Hidasi 等人](https://dl.acm.org/citation.cfm?id=2959167)提出了一种并行架构，用三个 GRU 学习用户id、图片和文本的特征向量。三个 GRU 的输出经过加权之后连接起来，用于预测当前会话的下一个产品。[Smirnova 等人](https://arxiv.org/pdf/1706.07684.pdf)在输入和输出层中插入上下文信息，提高推荐精度。
- [Recurrent Recommender Network](http://alexbeutel.com/papers/rnn_wsdm2017.pdf): RRN 是一个在 RNN 基础上构建的非参数推荐模型。它可以建模产品的季节性变化和用户偏好随时间的变化。它用两个 LSTM 对动态的用户状态和产品状态进行建模。同时考虑到用户的长期兴趣和产品的固有属性，模型中还引入了用户和产品的静态隐式因子。[Wu 等人](https://openreview.net/pdf?id=Bkv9FyHYx)在 RRN 基础上对文本评论和评分进行建模。该模型希望用字符级别的 LSTM，通过用户和产品的隐状态生成评论。评论生成任务可以看作一个帮助加强评分预测的辅助任务。但是，这个模型无法生成可读的评论文本。
- [Neural Survival Recommender](https://dl.acm.org/citation.cfm?id=3018719): 这项工作是一个多任务学习框架，意在预测用户和推荐产品的返回时间。作者用一个 LSTM 估计用户的返回时间，用另一个 LSTM 学习用户的历史会话动作。
- [Attention based RNN](https://pdfs.semanticscholar.org/0087/d3ee3e66498483dbc9e799f2744f562a75b7.pdf): 这项工作用 LSTM + att 获取微博中的信息，还用 LDA 学习微博的主题分布。主题分布用来学习注意权重。

#### RNN + 传统

- GRU Multitask Learning: [Bansal 等人](https://arxiv.org/pdf/1609.02116.pdf)用 GRU 将文本序列编码到隐因子模型中。作者用一个多任务规范项来防止过拟合，减少训练数据的稀疏性。主任务是评分预测，辅助任务是产品的元数据（标签、样式）预测。[Dai 等人](https://www.cc.gatech.edu/~lsong/papers/DaiWanTriSon16.pdf)提出一个隐式模型来获取用户和产品隐式特征的共同进化。用户和产品之间的相互作用会驱动用户偏好的产品特征的变化。为了对历史作用进行建模，作者提出用 RNN 自动学习用户和产品特征的趋势、进化和共同进化过程的影响。[Okura 等人](https://dl.acm.org/citation.cfm?id=3098108)提出用 GRU 学习用户的浏览历史，并用隐因子模型推荐新闻文章。相比于传统的基于词的方法，该推荐系统的精度有很大提升。

### 基于 DSSM

Deep Semantic Similarity Model (DSSM) 是信息检索领域常用的一种神经网络方法，极其适合用于 Top-N 推荐系统。DSSM 把不同的实体投影到同一个低维的投影空间中，通过余弦距离计算其相似度。

- [Deep Semantic Similarity based Personalized Recommendation (DSPR)](https://ora.ox.ac.uk/objects/uuid:f64f71ec-a0f3-4c0a-a793-f55e0215ddb3/download_file?file_format=application/pdf&safe_filename=shp0489-xuA.pdf&type_of_work=Conference%20item): 每个产品和用户都由其标签代替，由 DSSM 将其映射到统一的标签空间。用户和产品的相关程度用余弦相似度和 softmax 函数判别（余弦相似性通过softmax函数转化为概率形式）。
- [Multi-View Deep Neural Network (MV-DNN)]: MV-DNN 是一种为跨领域推荐设计的模型。它将用户看作中心视角（pivot view），每个领域看作辅助视角，其思想类似于 CCCFNet。但是，MV-DNN 基于一个基本假设，即在某一个领域偏好相同的用户在其他领域也有相同的偏好。但是这个假设在很多情况下都不成立。因此，想要充分利用 MV-DNN，需要在不同领域上用户偏好之间的关系方面有一些先验知识。

### 基于 RBM

- [Restricted Boltzmann Machine Collaborative Filtering (RBM-CF)]: [Salakhutdinov 等人](https://www.cs.toronto.edu/~rsalakhu/papers/rbmcf.pdf)首次提出了一种基于 RBM 的推荐系统。RBM 的可见单元只能取 0 或 1，因此评分需要被转化为 one-hot 向量（[0,0,0,1,0] 代表 4 分）。每个用户有一个带共享参数的 RBM。假设一个用户给 m 个电影打分，课件单元的个数就为 n。另外，作者提出用一个条件 RBM 来使用隐式反馈，在计算隐藏层的时候将隐式反馈向量包含进去。上面的两个 RBM-CF 都是把一个给定用户的评分放在可见层上。类似地，一个给定产品的评分也可以放在可见层上。[Ceorgiev 等人](http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=861BFAC5216E2800B3C1A257681F76BA?doi=10.1.1.310.3492&rep=rep1&type=pdf)提出将这两种 RBM-CF 融合到同一框架中。可见单元同时由用户和产品的隐藏单元决定。
- [Hybrid RBM-CF](https://dl.acm.org/citation.cfm?id=2959349): 这项工作把产品的特征（产品类别）加入到了 RBM-CF 中。它和条件 RBM-CF 的主要区别在于：（1）条件层由二值的产品类别建模；（2）条件层同时影响隐藏层和可见层，不同的连接方式具有不同的权重。

### 基于 NADE 和 GAN

Neural Autogressive Distribution Estimation (NADE) 是一种无监督神经网络，可以有效地对数据的分布和密度进行建模。目前 NADE 在评分预测任务中可以达到 state-of-the-art。GAN 融合了判决模型和生成模型，可以结合两种模型的优势。

- [NADE based collaborative filtering (CF-NADE)](https://arxiv.org/pdf/1605.09477.pdf): CF-NADE 用链式法则对用户评分进行建模：$p(\mathbf{\mathrm{r}} = \prod_{i=1}^{D} p(r_{m_{o_i}})\|\mathbf{\mathrm{r}}_{m_{o_{<i}}})$，其中 $D$ 是被评分的产品总数，$o$ 是 $(1, 2, \dots, D)$ 的一个排序，$\mathbf{\mathrm{r}}$ 是用户的评分向量，$r_{m_{o_i}}$ 是用户对产品 $m_{o_i}$ 的评分。理论上产品排序应该按照评分时间戳的顺序，但是从经验上来看，随机排序也能有很好的效果。

- [IRGAN](https://arxiv.org/pdf/1705.10513.pdf): IRGAN 是第一个把 GAN 应用到信息检索领域的工作。作者证明了 GAN 在三个子任务中的有效性：网络检索、产品推荐、问题回答。此处我们关注 IRGAN 在产品推荐中的应用。传统的 GAN 中包括一个判决器和一个生成器。类似地，在信息检索中也有两种相对的思路，生成式检索和判决式检索。生成式检索假设文档和查询串都有一个隐式的生成过程，给定一个查询串 q 能够生成一篇对应的文档 d，可以认为完成了信息检索任务。判决式检索则通过学习来预测给定的查询串-文档对的相关性分数。IRGAN 就是把两种思想放到一个统一的模型中，像 GAN 一样通过对抗来学习。生成式检索模块希望生成尽可能接近真实的相关文档，能够骗过判决式检索模块。在产品推荐任务中，作者用矩阵分解对两个函数进行建模：$g_\theta(q, d)$ 代表由查询串 q 生成文档 d 的概率；$f_\phi(q, d)$ 代表查询串 q 和文档 d 成对出现的相关性分数（经过一个sigmoid函数之后即为出现概率）。

## 多 DL 模型推荐系统

### CNN + AE

[Collaborative Knowledge Based Embedding (CKE)](https://dl.acm.org/citation.cfm?id=2939673) 用 CNN 和 AE 进行特征提取。CKE 可以看作是 CDL 的进一步发展。CDL 只考虑了产品的文本信息，而 CKE 利用了结构信息、文本信息和视觉信息，每种信息都采用了不同的嵌入技术。结构信息包括产品属性、产品和用户的关系；CKE 用 TransR 获取结构信息。同样，CKE 用 SDAE 获取文本信息的特征向量。对于视觉信息，CKE 用一个层叠的卷积自动编码器（stacked convolutional auto-encoder, SCAE）提取特征向量。SCAE 把 SDAE 中的全连接层替换成了卷积层。之后的推荐过程和 CDL 相同。

### CNN + RNN

[Lee 等人](https://dl.acm.org/citation.cfm?id=2914734&dl=ACM&coll=DL)提出用 CNN 和 RNN 组合构建引用推荐系统。CNN 用来学习 tweet 文本中的局部语义。局部语义向量送入 LSTM 计算目标引文和给定 tweet 对话的相关性。[Zhang 等人](https://www.ijcai.org/proceedings/2017/0478.pdf)用 CNN 和 RNN 的组合模型做 hashtag 推荐。给定一段 tweet 文本和对应的一组图片，作者用 CNN 提取图片特征，用 LSTM 学习文本特征。同时，作者提出了一个联合注意机制对文本和图片之间的关系和相互影响进行建模。[Ebsesu 等人](https://dl.acm.org/citation.cfm?id=3080730)在引文推荐系统的编码器-解码器框架中集成了 CNN 和 RNN。CNN 扮演编码器的角色，从引文内容中获取长期信息；RNN 扮演解码器的角色，在已经给定一篇被引用的文章标题的前面一截词语之后，学习下一个出现的词语的概率分布。

### CNN + MLP

[Lei 等人](https://arxiv.org/pdf/1604.01252.pdf)用一个融合了 CNN 和 MLP 的深层模型做图片推荐。网络中包括两个用于学习图片特征的 CNN 和一个用于建模用户偏好的 MLP。它比较同一个用户评价的两幅图片，一幅是用户喜欢的，另一幅是不喜欢的。训练数据由很多三元组构成：（用户，喜欢的图片，不喜欢的图片）。[ConTagNet](https://dl.acm.org/citation.cfm?id=2984068) 是一个基于上下文的标签推荐系统。CNN 用于学习图片特征。一个两层的 MLP 用于学习上下文特征。两个神经网络的输出连接在一起，经过softmax得到候选标签的概率分布。

### RNN + AE

前面提到的协同深度学习模型缺少鲁棒性，对文本序列的建模能力较弱。[Wang 等人](https://arxiv.org/pdf/1611.00454.pdf)探索用 RNN 和 denoising autoencoder 结合来克服以上限制。作者首先设计了 RNN 的一个变种：鲁棒 RNN。在此基础上，作者提出了一个分层贝叶斯推荐模型，称为 CRAE。其中包括编码器和解码器部分，但是用 RNN 代替了传统的前馈神经网络，使得 CRAE 能够获取产品描述的序列性信息。

### RNN + MLP

[NRT](https://arxiv.org/pdf/1708.00154.pdf) 是一个融合了 RNN 和 MLP 的多任务框架，既可以预测分数，又可以对用户生成文本提醒（tips）。分数预测任务是由 MLP 作用在用户和产品的隐因子矩阵上完成的。预测的评分和两个隐因子矩阵送入 GRU 用于生成 tips。

### CNN + DSSM

[Gao 等人](https://www.microsoft.com/en-us/research/wp-content/uploads/2014/10/604_Paper.pdf)提出用 CNN 和 DSSM 给用户推荐文档（基于已经阅读过的文档）。不同于 DSSM 和 DSPR，作者在隐藏层中加入了卷积和池化操作。卷积和池化层分别用于获取局部和全局信息。

### RNN + DSSM

[Song 等人](http://sonyis.me/paperpdf/spr209-song_sigir16.pdf)设计了一个集成 RNN 的 DSSM 用于推荐。在传统 DSSM 基础上，这项工作用产品的静态特征代替左网络，用两个子网络（分别对用户的静态特征和用户的动态特征进行建模）代替右网络。动态特征的加入使得模型参数量增加，因此作者用预训练技术加速参数学习过程。

## 未来研究方向

- 加深对用户真实需求和产品特征的理解
- 深层组合模型
- 时变动态特征
- 跨领域推荐
- 多任务学习
- 注意机制的使用
- 可扩展性
- 采用更多的评估指标

