---
title: 推荐算法的发展过程
date: 2021-11-17 14:22:35
tags: [推荐系统,机器学习,深度学习]
categories: 人工智能
widgets: null
password: 328
abstract: 这是一篇加密博文，请输入密码后查看
message: 这里需要密码才能访问。
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
---

## 第一章：互联网的增长引擎 - 推荐系统

首先是引入推荐系统，介绍其在互联网中的地位和作用，以及推荐系统的主要架构。作者将推荐系统的架构分为两部分：数据部分和模型部分。数据部分即离线大数据平台，实时 / 准实时的流处理平台。模型部分则包含召回、排序、补充策略、离线评估、线上 A/B test 等。

第二章：推荐系统的进化之路
-------------

这一章主要是传统推荐模型（机器学习类）的发展与演化。

![](https://pic3.zhimg.com/v2-3018cb8019e5dba2b2592fd244d6c916_r.jpg)

传统推荐模型的发展主要经历了四个阶段：

1.  **协同过滤 CF 算法阶段**：只需用户物品共现矩阵就可以构建推荐系统，根据相似度取值对象可分为 itemCF 和 userCF 两类，优势是简单易实现。CF 的问题是泛化能力弱，无法应对稀疏矩阵，而矩阵分解作为协同过滤的进化版，克服了 CF 的缺点。
2.  **逻辑回归 LR 阶段**：综合利用用户、物品、上下文等多种不同的特征，假设用户是否点击广告服从伯努利分布，将推荐问题转化为点击率预估 (CTR) 问题，预测正样本概率对物品进行排序。其数学形式是各个特征的加权和经过 sigmoid 函数，得到用户点击物品的概率。LR 的优势是可解释性强、易于并行化、模型简单、训练开销小。其局限性在于表达能力不强，需要大量具有业务背景知识的人工特征筛选与交叉。
3.  **因子分解机 FM 阶段**：为每个特征学习一个隐向量，在特征交叉时，使用两个特征隐向量的内积作为交叉特征的权重。虽然 FM 相比 POLY2 的完全交叉 + 单一权重记忆能力略弱，但解决了特征交叉过程中交叉特征对应的数据过于稀疏无法充分学习权重的问题。FFM 引入特征域进一步增强了模型的表达能力，做特征交叉时，每个特征选择与对方域对应的隐向量的内积作为交叉特征的权重，但 FFM 的计算复杂度也由 kn 上升到 kn*n。
4.  **组合模型阶段**：这一阶段主要是为了进一步提高特征交叉的维度，同时融合多个模型的优点。GBDT+LR 是组合模型的代表方案，GBDT 自动进行特征筛选和组合得到新的离散特征向量输入 LR 模型。GBDT+LR 的组合方式开启了特征工程模型化的趋势，真正实现端到端训练。

这一章的最后作者单独介绍了阿里巴巴曾经的主流推荐模型：大规模分段线性模型 (LS-PLM)，又称为 MLR(Mixed Logistic Regression，混合逻辑回归)。该模型从淘宝的业务特性出发，对不同用户群体、不同使用场景下的样本单独进行 CTR 预估。具体方法是先对全量样本聚类，再对每个分类里的样本使用 LR 进行 CTR 预估。

LS-PLM 模型的一大优势是端到端的非线性学习能力，样本聚类的方式能够让模型学习出数据中蕴藏的非线性模式，这里个人理解是 LS-PLM 模型的非线性学习能力强度还依赖于根据自身业务特性调节的超参 “分片数”。LS-PLM 因建模时引入了 L1 和 L2 范数，其中 L1 范数使得模型具有较高的稀疏度，这样一来，模型服务过程仅需使用非零权重，在线推断的效率很高。

作者认为 LS-PLM 在推荐系统模型发展的历史中起到了承上启下的作用。LS-PLM 可以看做一个加入了注意力机制的三层神经网络模型，其中输入层是样本的特征向量，中间层是 m 个神经元组成的隐层，其中 m 是分片的个数，最后一层是单一神经元组成的输出层。其中注意力机制应用在隐层和输出层之间，神经元之间的权重是由分片函数输出的概率值即注意力得分确定的。

第三章 深度学习在推荐系统中的应用
-----------------

推荐系统模型经过了机器学习阶段充分的发展后，终于进入了深度学习时代。与传统机器学习模型相比，深度学习模型具有表达能力更强，模型结构更灵活更贴合业务场景的优点。下图为深度学习阶段主流推荐模型的演化图谱：

![](https://pic2.zhimg.com/v2-5605c7f185be9e4fe3090cd74fd020d5_r.jpg)

正如图谱所示，深度学习阶段的推荐模型从多层感知机 MLP 出发，通过改变神经网络的结构，演变为各种各样的深度学习推荐模型。总结起来，有七个演变方向：

1.  **改变神经网络的复杂程度**：增加深度神经网络的层数和结构复杂度。
2.  丰富特征交叉方式：改变特征向量的交叉方式，如 NeuralCF，PNN(Product-based Neural Network)。
3.  **组合模型**：组合两种不同特点、优势互补的网络，主要是指 Wide&Deep 及其后续各种改进模型如 Deep&Cross、DeepFM 等。
4.  **FM 模型的深度学习演化**：对 FM 模型的各种改进，包括 NFM(Neural Factorization Machine) 使用神经网络提升 FM 二阶交叉部分的特征交叉能力、FNN(Factorization-machine supported Neural Network) 利用 FM 的结果进行网络初始化、AFM(Attention neural Factorization Machine) 在 FM 中引入注意力机制。
5.  **引入注意力机制**：主要包括上述的 AFM 和 DIN(Deep Interest Network， 深度兴趣网络) 模型
6.  **融合序列模型**：使用序列模型模拟用户行为或用户兴趣的演化趋势，如 DIEN(Deep Interest Evolution Network，深度兴趣进化网络)
7.  **结合强化学习**：主要是为了模型的在线学习和实时更新，包括 DRN(Deep Reinforcement Learning Network, 深度强化学习网络)

首先直接在 DNN 上演变的模型有：

*   **AutoRec**：将自编码器 (AutoEncoder) 与协同过滤结合的单隐层神经网络模型，利用协同过滤中的共现矩阵，完成物品 / 用户向量的自编码，基于自编码的结果得到用户对物品的预估评分，进而排序。AutoRec 模型结构和 word2vec 结构一致，相对简单，但优化目标和训练方法有所不同，AutoRec 表达能力有限。
*   **Deep Crossin**g：由微软于 2016 年发布，用于其搜索引擎 Bing 中的搜索广告推荐场景。Deep Crossing 完善了深度学习在推荐领域的实际应用流程，提出了一套完整的从特征工程、稀疏向量稠密化、多层神经网络进行优化目标拟合的解决方案，开启了无需任何人工特征工程的时代。其模型结构如下：

![](https://pic3.zhimg.com/v2-e1d333a6c3b203800193056f262468ce_r.jpg)

*   **NeuralCF**：2017 年的 NCF 用 “多层神经网络 + 输出层” 的结构替代了矩阵分解中的简单内积操作，让用户 / 物品向量做更充分的交叉，引入更多的非线性特征，增强模型表达能力。作者还提出一种 “广义矩阵分解”(Generalized Matrix Factorization) 模型融合了简单内积操作与多层神经网络两种特征交叉方式。NCF 模型同协同过滤一样只利用了用户物品的共现矩阵，并没有融合其他特征信息。模型结构如下图：

![](https://pic3.zhimg.com/v2-1c4b1be9fce83a3dd3d52bed244c3d7e_r.jpg)

*   **PNN**：2016 年的 PNN 模型在 Deep&Crossing 的基础上使用乘积层 (Product Layer) 代替 Stacking 层。即不同特征的 Embedding 向量不再是简单的拼接，而是通过 Product 操作两两交互。这里的 Product 操作包含两种：内积操作和外积操作。PNN 模型如下图所示：

![](https://pic3.zhimg.com/v2-b6579fba937a855a9e4e1ff1f251f7e6_r.jpg)

如上图所示， ![](https://www.zhihu.com/equation?tex=l_1+%3D+relu(l_z+%2B+l_p+%2B+b_1)) , 其中乘积层由两部分组成，z 来自于输入 Embedding 的线性变换，p 来自于上述 Product 操作的结果，这里 Product 如果选择的是内积，则称为 IPNN，若是外积，则称 OPNN。经过反复比对，书中关于这一点的描述和论文是有出入的，这里以论文为准。PNN 的出发点是丰富特征交叉方式，增强模型表达能力，而实际落地时，关于 OPNN 还是 IPNN 的选择以及各自对应的效率优化是需要根据业务场景权衡的。

*   **Wide&Deep**：Google 于 2016 年提出 Wide&Deep 模型，模型使用单输入层的 Wide 部分处理大量稀疏的 id 特征，提升记忆能力；使用 Embedding 和多隐层的 Deep 部分处理全量特征，赋予模型泛化能力。Wide 部分的输入特征除了原始的 id 特征（已安装应用和曝光应用）外，还包括转换后的特征，如叉乘变换 (Cross Product Transformation)，其实就是将单独的特征转换为组合特征，给模型增加非线性能力。

![](https://pic1.zhimg.com/v2-9f306df12ac12acfe98014a8ccc71f10_r.jpg)

*   **Deep&Cross**：斯坦福和 Google 合作基于 Wide&Deep 的改进。主要思路是使用 Cross 网络替代 Wide 部分，目的是通过多层交叉 (Cross layer) 增加特征之间的交互力度；Deep 部分则与 Wide&Deep 保持一致。模型结构如下图所示：

![](https://pic1.zhimg.com/v2-23a59d3c5f3c43dea960a9b1494016e4_r.jpg)

其中 Cross 网络的交叉方式与 OPNN 的外积操作十分类似， ![](https://www.zhihu.com/equation?tex=x_{l%2B1}+%3D+x_0+x_l^T+w_l+%2B+b_l+%2B+x_l+%3D+f+(x_l+%2C+w_l+%2C+b_l+)+%2B+x_l) , 其中 ![](https://www.zhihu.com/equation?tex=x_l%2C+x_{l%2B1}) 表示第 l 层和第 l+1 层 Cross 网络的输出， ![](https://www.zhihu.com/equation?tex=w_l%2C+b_l) 是第 l 层的权重和偏置。每一层 Cross 网络都会在特征交叉变换后加上该层的输入，即变换函数 f 拟合的是残差 ![](https://www.zhihu.com/equation?tex=x_{l%2B1}-x_l) 。具体过程如上述右图所示。  

Wide&Deep 融合传统模型的记忆能力和深度模型的泛化能力，工程实现简单高效，在很多公司都成功落地，也正是从 Wide&Deep 之后，深度推荐模型开始朝着结构多样化复杂化发展。其中之一就是 **FM 与深度学习结合产生出 FM 交叉特征系列的演变模型**：

*   **FNN**：出自 2016 年伦敦大学论文《Deep Learning over Multi-field Categorical Data – A Case Study on User Response Prediction》，相比 DNN，改进之处在于使用 FM 参数初始化其 Embedding 层的权重, ![](https://www.zhihu.com/equation?tex=y_{FM}(x)+%3D+sigmoid(w_0+%2B+\sum_{i%3D1}^N+w_i+x_i+%2B+\sum_{i%3D1}^N+\sum_{j%3Di%2B1}^N+<V_i%2C+V_j>x_i+x_j)) ，下图 Dense Real 层中的各权重 ![](https://www.zhihu.com/equation?tex=w_0%2C+w_i%2Cv_i%2C+w_j%2C+v_j) 与公式中是对应的，只是在训练 FM 的过程中，没有区分特征域，而 FNN 中特征被分成了不同的特征域，特征域内的 Embedding 是全连接，特征域之间无连接。论文中与 FNN 对比的是 SNN（Sparse Feature 层与 Embedding 层之间是全连接），此处不再赘述。

![](https://pic4.zhimg.com/v2-5a8b3240054381585819b80b1a564667_r.jpg)

*   **DeepFM**：2017 年由哈工大 & 华为提出，使用 FM 替换 Wide&Deep 的 Wide 部分，加强浅层网络组合特征的能力。DeepFM 的改进目的和 Deep&Cross 的目的是一致的，只是采用的手段不同。
*   **NFM**：2017 年新加坡国立大学提出 NeuralFM 模型，目的是使用表达能力更强的函数替换原本 FM 中二阶隐向量内积的部分，由 ![](https://www.zhihu.com/equation?tex=\hat+y_{FM}(x)+%3Dw_0+%2B+\sum_{i%3D1}^N+w_i+x_i+%2B+\sum_{i%3D1}^N+\sum_{j%3Di%2B1}^N+<V_i%2C+V_j>x_i+x_j) 变为 ![](https://www.zhihu.com/equation?tex=\hat+y_{NFM}(x)+%3D+w_0+%2B+\sum_{i%3D1}^N+w_i+x_i+%2B+f(x)) 。其中 f(x) 如下图所示。NFM 在 Embedding 层和多层神经网络之间增加了交叉池化层 (Bi-interaction Pooling layer)，用于将一组 Embedding 向量转换成一个向量，具体操作是对所有的 Embedding 向量做两两元素积，再对所有的元素积向量取和作为池化层的输出，输入上次的全连接网络。

![](https://pic1.zhimg.com/v2-72eb435c644f1ce92e4125e017294264_r.jpg)

**然后是 Attention 机制、序列模型与推荐系统的结合**：

*   **AFM**：AFM 既是 FM 系列模型的延续演化，也是 Attention 机制与推荐系统的结合发展。此处不再赘述。
*   **DIN**：阿里巴巴根据其典型的电商广告推荐场景，于 2017 年提出 DNN 结合 Attention 机制的 DIN(Deep Interest Network) 模型。利用候选商品和用户历史交互商品之间的相关性得出注意力权重，以此根据用户历史交互商品计算出用户的加权和 Embedding， ![](https://www.zhihu.com/equation?tex=V_u+%3D+f(V_a)+%3D+\sum_{i%3D1}^N+w_i+V_i+%3D+\sum_{i%3D1}^N+g(V_i%2C+V_a)+V_i) ，其中 ![](https://www.zhihu.com/equation?tex=V_u) 是用户的 Embedding 向量， ![](https://www.zhihu.com/equation?tex=V_a) 是候选广告商品的 Embedding 向量， ![](https://www.zhihu.com/equation?tex=V_i) 是用户 u 的第 i 次互动行为的 Embedding 向量， ![](https://www.zhihu.com/equation?tex=g(V_i%2C+V_a)) 为注意力得分，即候选广告与用户第 i 次行为的相似度。模型中注意力激活单元的设计是输入两个 Embedding 向量，经过元素减操作 (element-wise minus) 后，与原 Embedding 向量拼接后送入全连接层得出注意力分值。如下图右上角所示。

![](https://pic1.zhimg.com/v2-1df41a8ed74492d165cd5fc3c52d3aa0_r.jpg)

*   **DIEN**：2019 年阿里提出 DIEN(Deep Interest Envole Network) 在 DIN 的基础上融合了序列模型，用于模拟用户兴趣随时间的变化过程。在电商广告推荐场景中，序列信息加强了用户最近行为对下次行为预测的影响，能够学习用户的购买趋势。出于这样的目的，阿里巴巴在 DIN 的基础上引入了兴趣进化网络学习用户的兴趣 Embedding。模型结构图如下：

![](https://pic3.zhimg.com/v2-8ed831f5e61b4a47d257a5a7167e8fba_r.jpg)

DIEN 的兴趣进化网络分为三层：行为序列 (Behavior Layer) 层，把原始的 id 类行为序列转换成 Embedding 行为序列；兴趣抽取 (Interest Extractor Layer) 层，抽取用户兴趣，模拟用户兴趣迁移；兴趣进化 (Interest Evolving Layer) 层，增加注意力机制，模拟与目标广告相关的兴趣进化过程。其中兴趣抽取层的基本结构是 GRU(Gated Recurrent Unit)，将用户的行为向量 Embedding 转换成兴趣状态向量。兴趣进化层也是通过 AUGRU(GRU with Attentional Update gate)引入注意力机制的，在原 GRU 的更新门结构上增加了注意力得分。

*   **MIND**：DIEN 之后，阿里天猫团队又提出使用胶囊网络提取用户的多样兴趣，再加上基于标签的注意力机制，提升召回的丰富度和准确度，此模型并未包含在书中，但笔者认为 MIND 模型还是有很多值得探究的地方，也有实际落地的效果和意义。关于胶囊网络的理解感兴趣的小伙伴可自行阅读相关论文，此处只解释 MIND 模型中的用法。用户交互的物品 Embedding 集合作为多样兴趣提取 (Multi-interest Extractor Layer) 层的输入，经过胶囊网络的动态路由算法生成一组用户兴趣 Embedding(可以视为用户行为的软聚类)。用户基础画像属性的 Embedding 分别与用户兴趣 Embedding 拼接，经过两层全连接，得到一组用户 Embedding。再根据 label 的 Embedding 与用户 Embedding 求相似度作为注意力权重。MIND 网络结构如下图所示：

![](https://pic1.zhimg.com/v2-47e9dc8f58eec4787794e93ecb08e3f4_r.jpg)

最后是强化学习与推荐系统的融合：

*   **DRN**：强化学习相比传统深度模型的优势在于强化学习模型能够进行 “在线学习”，不断利用新学到的知识更新自己，及时调整和反馈。DRN(Deep Reinforcement Learning) 框架中起决定作用的是 DQN(Deep Q-Network), 对 DQN 感兴趣的小伙伴可以自行阅读相关论文，这里只说推荐系统中如何使用 DQN。DQN 把用户特征和环境特征归为和具体行动无关的状态向量，把用户 - 新闻交叉特征以及新闻特征归为与推荐内容这一行动相关的行动特征。DQN 的网络结构如下图所示。用户特征和环境特征经过左侧多层神经网络拟合得到价值得分 ![](https://www.zhihu.com/equation?tex=V(s)) ，利用状态向量和行动向量生成优势得分 ![](https://www.zhihu.com/equation?tex=A(s%2Ca)) , 综合二者分值，得到最终的质量得分 ![](https://www.zhihu.com/equation?tex=Q(s%2Ca)) 。

![](https://pic3.zhimg.com/v2-5dba35627be41c3034e893b67876a67a_r.jpg)

DRN 中使用的一种新的在线训练方法 - 竞争梯度下降算法 (Dueling Bandit Gradient Descent Algorithm) 是其能够在线学习的原因，其主要步骤如下：  

- 1) 对于已经训练好的当前网络 Q，对其模型参数 W 添加一个较小的随机扰动 ![](https://www.zhihu.com/equation?tex=\Delta+W) ，得到新的模型参数 ![](https://www.zhihu.com/equation?tex=\widetilde+W) ，这里 ![](https://www.zhihu.com/equation?tex=%5Cwidetilde+W) 即为探索网络 ![](https://www.zhihu.com/equation?tex=%5Cwidetilde+W) .

- 2) 对于当前网络 Q 和探索网络 ![](https://www.zhihu.com/equation?tex=%5Cwidetilde+W) ，分别生成推荐列表 L 和 ![](https://www.zhihu.com/equation?tex=\widetilde+L) ，用 Interleaving 方法将合并两个推荐列表后推送给用户。

- 3) 实时收集用户反馈，如果探索网络 ![](https://www.zhihu.com/equation?tex=\widetilde+Q) 的反馈好于当前网络 Q，则用探索网络替代当前网络，进入下一轮迭代；反之则保留当前网络。

## 第四章 Embedding 技术在推荐系统中的应用

推荐系统中的 Embedding 技术主要是指将高维稀疏特征向量转换成低维稠密向量，便于深度神经网络的处理，同时具有综合信息能力强、易于线上部署的特点。

首先是经典 Embedding 方法：

- **word2Vec**:2013 年由 Google 提出，从 nlp 推广到广告、搜索、图像、推荐等深度学习应用领域。w2v 是生成对 “词” 的向量表达的模型。对于由单词组成句子，句子组成文档的语料库，w2v 假设每个词都跟其相邻词组成的上下文相关, 或决定上下文 (Skip-gram) 或由上下文决定(CBOW)。对任意一个句子，目标词前后各选 c 个词作为上下文，在句子中滑动此长度 2c+1 的滑动窗口，每移动一次就是一个训练样本。

以 skip-gram 为例，既然每个词都决定了上下文中的词，根据极大似然估计，对于一个完整的句子，为了使滑动窗口内单词的条件概率 ![](https://www.zhihu.com/equation?tex=p(w_{t%2Bj}|w_t)) 之积最大，转换成对数概率后的目标函数为最大化平均对数概率: ![](https://www.zhihu.com/equation?tex=\frac+{1}{T}+\sum_{t%3D1}^T+\sum_{-c\leq+j+\leq+c%2C+j\neq+0}+logp(w_{t%2Bj}|w_t)) , 在原始 skip-gram 中条件概率 ![](https://www.zhihu.com/equation?tex=p%28w_%7Bt%2Bj%7D%7Cw_t%29) 使用 ![](https://www.zhihu.com/equation?tex=p(W_o|W_I)%3D\frac+{exp({V^{'}_{W_o}}^T+V_{W_I})}+{\sum_{w%3D1}^W+exp({V^{'}_w}+^T+V_{W_I})}) , 书中此处公式与论文中不同，本文以论文为准。 ![](https://www.zhihu.com/equation?tex=W_I) 为输入目标词，$W_O$ 指上下文单词。 ![](https://www.zhihu.com/equation?tex=v_w) 和 ![](https://www.zhihu.com/equation?tex=v^{'}_w) 分别为单词 w 的输入输出表示，这里每个单词都有一个输入表示对应下图网络结构中的 ![](https://www.zhihu.com/equation?tex=W_{V\times+N}) 参数中的 Embedding，与一个输出表示对应 ![](https://www.zhihu.com/equation?tex=W^{'}_{N\times+V}) 参数中的 Embedding，所以前面的 ![](https://www.zhihu.com/equation?tex=p%28w_%7Bt%2Bj%7D%7Cw_t%29) 是用目标词的左侧 Embedding 与上下文各单词的右侧 Embedding 的乘积做 softmax。

![](https://pic2.zhimg.com/v2-a3c730615f68055317746b285190ebbd_r.jpg)

word2vec 有两种加速训练方法：负采样和 Hierarchical softmax，其中负采样实现简单高效，使用比较广泛。原本负样本集合为词典中除目标词外的所有词，计算复杂度高，只采样其中几个作为负样本可大幅提高训练速度也不会降低训练效果。

- **Item2Vec**: 2016 年微软提出的 Item2Vec 与 Word2vec 基本类似，只是把背景从 nlp 推广到推荐领域。Item2Vec 把用户的浏览、购买等行为交互的物品列表作为 "句子"。Item2Vec 与 Word2Vec 唯一的不同在于，Item2Vec 中没有时间窗口一说，即用户交互的物品序列中任意两个物品都相关，因此目标函数也是最大化序列中两两物品的对数概率之和: ![](https://www.zhihu.com/equation?tex=\frac+{1}{K}+\sum_{i%3D1}^K+\sum_{j\neq+i}^K+logp(w_j+|+w_i)) 。这里作者将 Item2Vec 扩展到了广义上的 “双塔” 中的“物品塔”，都是将物品相关的原始特征转换为物品的 Embedding 向量。Item2Vec 是学习序列物品表示的最优选择，但序列数据也是 Item2Vec 的局限所在。

经典 Embedding 方法都存在只能应用在序列数据的局限性，而互联网场景下更多的是网络图结构的数据，就有了 graph embedding-- 学习图中节点 Embedding 的方法：

- **DeepWalk**：2014 年被提出，主要思路是在物品组成的图结构上随机游走，产生大量物品序列，然后基于这些物品序列进行 Word2Vec 训练得到物品的 Embedding。DeepWalk 随机游走过程中的跳转概率可根据业务灵活定义。

![](https://pic1.zhimg.com/v2-5a5687b9647a3770b9c043c2dad115d0_r.jpg)

- **LINE**：为提升大规模网络图结构上节点 Embedding 学习的效率，2015 年微软 & 北大提出了 LINE 模型。论文还提出了衡量图中节点的两种相似度的两种指标：一阶相似度 (first-order proximity) 即有直接连接的顶点之间是相似的， ![](https://www.zhihu.com/equation?tex=p_1+(v_i%2C+v_j)+%3D+\frac+{1}+{1%2Bexp(-\stackrel{\rightarrow}{u_i}^T++\stackrel{\rightarrow}{u_j})}) ；二阶相似度 (second-order proximity) 邻近网络结构的相似性即共享邻居节点的节点是相似的, ![](https://www.zhihu.com/equation?tex=p_2(v_j+|+v_i)%3D\frac{exp(\stackrel{\rightarrow}{u_j}^{'T}+\stackrel{\rightarrow}{u_i})}+{\sum_{k%3D1}^{|V|}+exp(\stackrel{\rightarrow}{u_k}^{'T}+\stackrel{\rightarrow}{u_i})}) 。学习节点 Embedding 时通过保留这两种相似度来保留原始的图结构信息。为降低复杂度，论文采用分别训练一阶相似模型和二阶相似模型，然后将两个向量拼接成一个更长的向量的方法，并使用负采样、边采样等方法优化模型速度。

- **Node2Vec**: 2016 年斯坦福提出图数据结构的两种性质：网络的同质性 (homophily)，距离相近的节点的 Embedding 应尽量相似；网络的结构性 (structural equivalence)，结构上相似的节点的 Embedding 应尽量相似。这两种性质可以认为是一阶二阶相似度的扩展，在推荐系统中的解释是，同质性相同的物品很可能是同品类、同属性或经常被一同购买的物品，而结构性相同则是各品类中的爆款、最佳凑单物品。Node2Vec 通过调整随机游走权重控制节点间跳转概率进而决定节点 Embedding 结果倾向于同质性还是结构性。从节点 v 跳转到下一个节点 x 的概率 ![](https://www.zhihu.com/equation?tex=\pi_{vx}+%3D+\alpha_{pq}(t%2C+x)+\omega_{vx}%24%2C+%24\alpha_{pq}(t%2C+x)) 定义如下图。

![](https://pic2.zhimg.com/v2-c862583f310957a28e818b93004c4d6d_r.jpg)![](https://pic3.zhimg.com/v2-c8dc410e8cbcf1e1a10a826e3eca2a0e_b.jpg)

- **EGES**: 2018 年阿里巴巴公布淘宝使用的 graph Embedding 方法，EGES(Enhanced Graph Embedding with side Information)，基本思想是在 Deepwalk 生成的 Graph Embedding 基础上引入节点的其他属性信息作为冷启动时的补充信息，因此一个节点可能拥有多个 Embedding 向量，EGES 将这多个向量加权平均后输入 softmax 层，学习出每个 Embedding 的权重。

上述这些 GraphEmbedding 方法可以认为都是直推式学习 (transductive learning), 即在固定的图上学习每个节点的 Embedding，每次学习只考虑当前数据，不能适应图结构经常变化的情况，因为图结构发生变化后需要重新学习全图 Embedding。而归纳学习(inductive learning) 是学习在图上生成节点 Embedding 的方法，这一类中目前已经落地的是 Pinsage 算法：

- **GraphSage & PinSage**：2017 年斯坦福发表 GraphSage 论文，2018 年斯坦福和 Pinterest 公司合作落地 PinSage。GraphSage 的核心是学习如何聚合节点的邻居特征生成当前节点的信息，学习到这样一个聚合函数之后，不管图结构和图信息如何变化，都可以通过当前已知各个节点的特征和邻居关系，生成节点的 embedding。GraphSage 算法主要由两个操作组成：Sample 采样和 Aggregate 聚合。采样是为了避免全图计算，每次只计算部分节点的 Embedding。聚合操作则是学习一个聚合函数。关于 GraphSage 和 PinSage 的详细讲解，感兴趣的小伙伴可以参考：

[一块小蛋糕：全面理解 PinSagezhuanlan.zhihu.com![图标](https://pic3.zhimg.com/v2-e79d1ead9bdb0bdb47641344938fc3f6_180x120.jpg)](https://zhuanlan.zhihu.com/p/133739758)

最后是 Embedding 在推荐系统中的使用方式：

- **转换高维稀疏特征**：使用深度模型处理高维稀疏特征向量几乎都会在输入层到全连接层之间加入 Embedding 层，将高维稀疏特征转换为低维稠密 Embedding。但 Embedding 层存在一个缺点，维度超高导致模型收敛速度变慢。

- **预训练**：将 Embedding 的训练单独进行，得到稀疏特征的稠密表达后与其他特征一起送入神经网络训练。比如 Graph Embedding 的训练过程一般都是独立于推荐模型的。

- **召回**：利用 Embedding 进行候选物品召回，代表方案是 YouTube 推荐召回层的方式，模型部署时无需部署整个深度神经网络，只需将用户 Embedding 和物品 Embedding 存储到线上内存数据库通过内积运算再排序的方式就可以得到物品的排序。

- **在线服务**：以高维空间快速搜索最近邻操作代替 Embedding 之间的内积运算。工程实践上主流的快速 Embedding 向量最近邻搜索方法：局部敏感哈希 (Locality Sensitive Hashing)，其基本思想是让相邻的点落入同一个 bucket，在进行最近邻搜索时，只需搜索一个或几个 bucket 即可。LSH 背后的理论依据是低维空间可以部分保存高维空间的近似距离信息。LSH 采用多个哈希函数同时进行分桶，在多桶集合的并集或交集组成的相邻点的候选集合中搜索 K 近邻。

第五章 多角度审视推荐系统
-------------

这一章主要是总结了推荐领域的一些特有问题。

- **特征选取和处理**：推荐系统特征工程应该遵循的原则是尽可能地让特征工程抽取出一组特征能够保留推荐环境及用户行为过程中的所有有用信息，尽量摒弃冗余信息。推荐系统中常用的特征数据包括用户行为数据、用户关系数据、属性标签类数据、内容类数据、上下文信息、统计类特征、组合类特征。常用的特征处理方法包括连续特征归一化、离散化、加非线性函数；类别特征 one-hot 或 multi-hot 编码等。

- **召回层策略**：多路召回、基于 Embedding 召回。

- **推荐系统实时性**：推荐系统的实时性体现在能实时地感知用户反馈满足用户期望。这里的实时性包含两大要素: 特征实时性，即实时地收集和更新推荐模型的输入特征使推荐系统总能以最新的特征进行预测和推荐；模型实时性，即更快地捕获全局最新数据模式、发现数据中的新趋势和相关性。模型的实时性与模型的更新方式相关：全量更新、增量更新和在线学习。而实际应用中除了这三种更新方式还有模型的局部更新及客户端模型实时更新（更新用户自身 Embedding）两种方式。

- **设定推荐模型的优化目标**：合理的推荐系统优化目标由公司的商业目标决定，确保模型的优化方向与应用场景保持一致。例如 YouTube 以观看时长为优化目标，电商场景以 CVR、CTR 为优化目标。

- **根据业务改进模型结构**：推荐模型的结构不是构建一个好的推荐系统的 “银弹”，对用户行为和应用场景的观察分析才是。以 Netflix 和阿里巴巴对各自推荐模型结构的改进为例，都是根据自家业务场景分析的结果。

- **冷启动问题**：主要是用户冷启动、物品冷启动、系统冷启动三种。主流的冷启动策略有基于规则的冷启动过程、丰富冷启动过程中可获得的用户和物品特征、利用主动学习以及迁移学习和探索利用机制。

- **探索与利用策略**：E&E 策略主要用于解决冷启动问题、挖掘用户新兴趣、增加推荐结果的多样性。传统的探索与利用方法有 ![](https://www.zhihu.com/equation?tex=\epsilon-greedy) 、Thompson Sampling 和 UCB。这类方法中不考虑用户上下文等因素。个性化的探索与利用策略主要是 LinUCB。而基于模型的探索与利用方法主要是 DRN。

第六章 深度学习推荐系统的工程实现
-----------------

实现深度学习推荐流的一些工程问题

- **数据流工程**：基于 Lambda 架构或 Kappa 架构的数据平台加入机器学习层，将机器学习与数据处理融为一体。

- **模型的离线训练**：分布式机器学习训练有三个主流方案：Spark MLib、Parameter Server 和 TensorFlow。其中 Spark MLib 的模型并行训练过程是 “数据并行” 的过程，采用全局广播的方式在每轮迭代前广播全部模型参数，同步更新梯度，不支持复杂深度学习网络和大量可调试超参。

而 Parameter Server 方案则完美解决了机器学习模型的分布式训练问题，其中的 server 节点保存模型参数、接收 worker 节点计算出的局部梯度、汇总计算全局梯度并更新模型参数；worker 节点保存部分训练数据，从 server 节点拉取最新的模型参数，根据训练数据计算局部梯度，上传给 server 节点。worker 之间，server 之间并不通信，只有 worker 和 server 之间存在数据交流。相比 spark 只支持同步更新梯度，PS 架构既支持同步更新也支持异步更新策略，同时使用一致性哈希、参数范围拉取、参数范围推送等工程手段避免广播造成全局网络阻塞。

TensorFlow 中基于各种操作 op 组成的任务关系图进行并行训练，存在依赖关系的任务节点或子图之间需要串行执行，不存在依赖关系的任务节点或子图之间可以并行执行。tf 的分布式策略有多种，一般使用的是 Parameter Server 策略，和 PS 方案一致。

- **模型的线上部署**：最简单直接的方法是将离线环境下生成的每个用户的推荐结果预存到线上数据库如 Redis 等。好处是无需线上推断且服务延迟极低。但无法提供实时推荐效果且随着用户增长线上资源要求越来越高。其次是预训练 Embedding + 轻量级线上模型，线上预存用户和物品的 Embedding，推断时只实现最后输出层的逻辑。还有一个选择是利用 PMML 转换并部署模型，例如使用 Spark 训练完模型后保存为 PMML 文件，Java Server 进行 PMML 文件的解析并恢复模型进行推断。最后一个选择就是 TensorFlow Serving，其服务流程和 PMML 一致，只是模型文件是 tf 自带的序列化标准。

第七章 推荐系统的评估
-----------

推荐系统的评估用于指导推荐系统的优化方向：

- **离线评估**：主要有 Holdout 检验，交叉检验，自助法三种。模型离线评估指标包括准确率、精确率、召回率、均方根误差、对数损失等。推荐序列的离线评估指标则包括 P-R 曲线，ROC 曲线，平均精度均值 mAP，归一化折扣累计收益 NDCG，覆盖率，多样性等。

- **离线仿真评估 Replay**：生成每天的数据快照，使用历史样本数据进行重放。Replay 适用于几乎所有推荐模型的离线评估，而且是强化学习类模型唯一的离线评估方法。

- **线上 A/B test**：利用控制变量法保持单一变量的前提下，将 A，B 两组数据对比得出实验结论 。相比于离线评估无法消除数据有偏影响、无法完全还原线上环境、无法计算一些线上商业指标 (如用户点击率，留存时长，PV 访问量等)。A/B test 测试原则：层与层之间的流量正交，即实验中每组的流量穿越该层后都会被再次随机打散，且均匀分布在下个实验的每个实验组中；同层之间的流量互斥，即同层不同测试之间的流量不重叠。

- **快速线上评估 - Interleaving**：线上 A/B test 的预选阶段，能够快速地将最初的候选算法集合进行缩减，比 A/B test 能更快确定最优算法。Interleaving 是把不同的被测对象同时提供给受试者，最后根据受试者喜好得出评估结果。优点是所需样本少、测试速度快、结果与 A/B test 无明显差异。虽然 Interleaving 指标与 A/B test 测试评估指标之间存在非常强的相关性，但 Interleaving 无法完全替代 A/B test。



