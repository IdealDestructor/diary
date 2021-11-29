---
title: 推荐算法的发展过程
date: 2021-11-17 14:22:35
tags: [推荐系统,机器学习,深度学习]
categories: 人工智能
widgets: null
---

推荐系统的架构分为两部分：数据部分和模型部分。数据部分即离线大数据平台，实时 / 准实时的流处理平台。模型部分则包含召回、排序、补充策略、离线评估、线上 A/B test 等。推荐算法则可以分为传统推荐算法和深度学习推荐算法。

<!--more-->


1. 推荐算法分类及其比较

   ![img](https://pic3.zhimg.com/v2-3018cb8019e5dba2b2592fd244d6c916_r.jpg)

   一、传统推荐模型
   
   1. 协同过滤CF算法
      只需用户物品共现矩阵就可以构建推荐系统，根据相似度取值对象可分为 itemCF 和 userCF 两类，优势是简单易实现。CF 的问题是泛化能力弱，无法应对稀疏矩阵，而矩阵分解作为协同过滤的进化版，克服了 CF 的缺点。
   2. 逻辑回归LR算法
      综合利用用户、物品、上下文等多种不同的特征，假设用户是否点击广告服从伯努利分布，将推荐问题转化为点击率预估 (CTR) 问题，预测正样本概率对物品进行排序。其数学形式是各个特征的加权和经过 sigmoid 函数，得到用户点击物品的概率。LR 的优势是可解释性强、易于并行化、模型简单、训练开销小。其局限性在于表达能力不强，需要大量具有业务背景知识的人工特征筛选与交叉。
   3. 因子分解机 FM算法
      为每个特征学习一个隐向量，在特征交叉时，使用两个特征隐向量的内积作为交叉特征的权重。虽然 FM 相比 POLY2 的完全交叉 + 单一权重记忆能力略弱，但解决了特征交叉过程中交叉特征对应的数据过于稀疏无法充分学习权重的问题。FFM 引入特征域进一步增强了模型的表达能力，做特征交叉时，每个特征选择与对方域对应的隐向量的内积作为交叉特征的权重，但 FFM 的计算复杂度也由 kn 上升到 kn*n。
   4. 组合模型
      这一阶段主要是为了进一步提高特征交叉的维度，同时融合多个模型的优点。GBDT+LR 是组合模型的代表方案，GBDT 自动进行特征筛选和组合得到新的离散特征向量输入 LR 模型。GBDT+LR 的组合方式开启了特征工程模型化的趋势，真正实现端到端训练。

   二、深度学习推荐算法

   ![](https://i.loli.net/2021/11/18/a8LFcflVOgbPSCj.png)
   
   深度学习阶段的推荐模型从多层感知机 MLP 出发，通过改变神经网络的结构，演变为各种各样的深度学习推荐模型。总结起来，有七个演变方向：
   
   1. **改变神经网络的复杂程度**：增加深度神经网络的层数和结构复杂度。
   2. 丰富特征交叉方式：改变特征向量的交叉方式，如 NeuralCF，PNN(Product-based Neural Network)。
   3. **组合模型**：组合两种不同特点、优势互补的网络，主要是指 Wide&Deep 及其后续各种改进模型如 Deep&Cross、DeepFM 等。
   4. **FM 模型的深度学习演化**：对 FM 模型的各种改进，包括 NFM(Neural Factorization Machine) 使用神经网络提升 FM 二阶交叉部分的特征交叉能力、FNN(Factorization-machine supported Neural Network) 利用 FM 的结果进行网络初始化、AFM(Attention neural Factorization Machine) 在 FM 中引入注意力机制。
   5. **引入注意力机制**：主要包括上述的 AFM 和 DIN(Deep Interest Network， 深度兴趣网络) 模型
   6. **融合序列模型**：使用序列模型模拟用户行为或用户兴趣的演化趋势，如 DIEN(Deep Interest Evolution Network，深度兴趣进化网络)
   7. **结合强化学习**：主要是为了模型的在线学习和实时更新，包括 DRN(Deep Reinforcement Learning Network, 深度强化学习网络)

   首先直接在 DNN 上演变的模型有：

   - **AutoRec**：将自编码器 (AutoEncoder) 与协同过滤结合的单隐层神经网络模型，利用协同过滤中的共现矩阵，完成物品 / 用户向量的自编码，基于自编码的结果得到用户对物品的预估评分，进而排序。AutoRec 模型结构和 word2vec 结构一致，相对简单，但优化目标和训练方法有所不同，AutoRec 表达能力有限。

   - **Deep Crossin**g：由微软于 2016 年发布，用于其搜索引擎 Bing 中的搜索广告推荐场景。Deep Crossing 完善了深度学习在推荐领域的实际应用流程，提出了一套完整的从特征工程、稀疏向量稠密化、多层神经网络进行优化目标拟合的解决方案，开启了无需任何人工特征工程的时代。其模型结构如下：

   - **NeuralCF**：2017 年的 NCF 用 “多层神经网络 + 输出层” 的结构替代了矩阵分解中的简单内积操作，让用户 / 物品向量做更充分的交叉，引入更多的非线性特征，增强模型表达能力。作者还提出一种 “广义矩阵分解”(Generalized Matrix Factorization) 模型融合了简单内积操作与多层神经网络两种特征交叉方式。NCF 模型同协同过滤一样只利用了用户物品的共现矩阵，并没有融合其他特征信息。模型结构如下图：

   - **PNN**：2016 年的 PNN 模型在 Deep&Crossing 的基础上使用乘积层 (Product Layer) 代替 Stacking 层。即不同特征的 Embedding 向量不再是简单的拼接，而是通过 Product 操作两两交互。这里的 Product 操作包含两种：内积操作和外积操作。PNN 模型如下图所示：

   - **Wide&Deep**：Google 于 2016 年提出 Wide&Deep 模型，模型使用单输入层的 Wide 部分处理大量稀疏的 id 特征，提升记忆能力；使用 Embedding 和多隐层的 Deep 部分处理全量特征，赋予模型泛化能力。Wide 部分的输入特征除了原始的 id 特征（已安装应用和曝光应用）外，还包括转换后的特征，如叉乘变换 (Cross Product Transformation)，其实就是将单独的特征转换为组合特征，给模型增加非线性能力。

   - **Deep&Cross**：斯坦福和 Google 合作基于 Wide&Deep 的改进。主要思路是使用 Cross 网络替代 Wide 部分，目的是通过多层交叉 (Cross layer) 增加特征之间的交互力度；Deep 部分则与 Wide&Deep 保持一致。模型结构如下图所示：

     其中 Cross 网络的交叉方式与 OPNN 的外积操作十分类似， ![img](https://www.zhihu.com/equation?tex=x_%7Bl%2B1%7D+%3D+x_0+x_l%5ET+w_l+%2B+b_l+%2B+x_l+%3D+f+%28x_l+%2C+w_l+%2C+b_l+%29+%2B+x_l) , 其中 ![img](https://www.zhihu.com/equation?tex=x_l%2C+x_%7Bl%2B1%7D) 表示第 l 层和第 l+1 层 Cross 网络的输出， ![img](https://www.zhihu.com/equation?tex=w_l%2C+b_l) 是第 l 层的权重和偏置。每一层 Cross 网络都会在特征交叉变换后加上该层的输入，即变换函数 f 拟合的是残差 ![img](https://www.zhihu.com/equation?tex=x_%7Bl%2B1%7D-x_l) 。具体过程如上述右图所示。

     Wide&Deep 融合传统模型的记忆能力和深度模型的泛化能力，工程实现简单高效，在很多公司都成功落地，也正是从 Wide&Deep 之后，深度推荐模型开始朝着结构多样化复杂化发展。其中之一就是 **FM 与深度学习结合产生出 FM 交叉特征系列的演变模型**：

   - **FNN**：出自 2016 年伦敦大学论文《Deep Learning over Multi-field Categorical Data – A Case Study on User Response Prediction》，相比 DNN，改进之处在于使用 FM 参数初始化其 Embedding 层的权重，![img](https://www.zhihu.com/equation?tex=y_%7BFM%7D%28x%29+%3D+sigmoid%28w_0+%2B+%5Csum_%7Bi%3D1%7D%5EN+w_i+x_i+%2B+%5Csum_%7Bi%3D1%7D%5EN+%5Csum_%7Bj%3Di%2B1%7D%5EN+%3CV_i%2C+V_j%3Ex_i+x_j%29) ，下图 Dense Real 层中的各权重 ![img](https://www.zhihu.com/equation?tex=w_0%2C+w_i%2Cv_i%2C+w_j%2C+v_j) 与公式中是对应的，只是在训练 FM 的过程中，没有区分特征域，而 FNN 中特征被分成了不同的特征域，特征域内的 Embedding 是全连接，特征域之间无连接。论文中与 FNN 对比的是 SNN（Sparse Feature 层与 Embedding 层之间是全连接)，此处不再赘述。

   - **DeepFM**：2017 年由哈工大 & 华为提出，使用 FM 替换 Wide&Deep 的 Wide 部分，加强浅层网络组合特征的能力。DeepFM 的改进目的和 Deep&Cross 的目的是一致的，只是采用的手段不同。

   - **NFM**：2017 年新加坡国立大学提出 NeuralFM 模型，目的是使用表达能力更强的函数替换原本 FM 中二阶隐向量内积的部分，由 ![img](https://www.zhihu.com/equation?tex=%5Chat+y_%7BFM%7D%28x%29+%3Dw_0+%2B+%5Csum_%7Bi%3D1%7D%5EN+w_i+x_i+%2B+%5Csum_%7Bi%3D1%7D%5EN+%5Csum_%7Bj%3Di%2B1%7D%5EN+%3CV_i%2C+V_j%3Ex_i+x_j) 变为 ![img](https://www.zhihu.com/equation?tex=%5Chat+y_%7BNFM%7D%28x%29+%3D+w_0+%2B+%5Csum_%7Bi%3D1%7D%5EN+w_i+x_i+%2B+f%28x%29) 。其中 f(x) 如下图所示。NFM 在 Embedding 层和多层神经网络之间增加了交叉池化层 (Bi-interaction Pooling layer)，用于将一组 Embedding 向量转换成一个向量，具体操作是对所有的 Embedding 向量做两两元素积，再对所有的元素积向量取和作为池化层的输出，输入上次的全连接网络。

     
   
   然后是 Attention 机制、序列模型与推荐系统的结合：
   
   - **AFM**：AFM 既是 FM 系列模型的延续演化，也是 Attention 机制与推荐系统的结合发展。此处不再赘述。
   - **DIN**：阿里巴巴根据其典型的电商广告推荐场景，于 2017 年提出 DNN 结合 Attention 机制的 DIN(Deep Interest Network) 模型。利用候选商品和用户历史交互商品之间的相关性得出注意力权重，以此根据用户历史交互商品计算出用户的加权和 Embedding， ![img](https://www.zhihu.com/equation?tex=V_u+%3D+f(V_a)+%3D+\sum_{i%3D1}^N+w_i+V_i+%3D+\sum_{i%3D1}^N+g(V_i%2C+V_a)+V_i) ，其中 ![img](https://www.zhihu.com/equation?tex=V_u) 是用户的 Embedding 向量， ![img](https://www.zhihu.com/equation?tex=V_a) 是候选广告商品的 Embedding 向量， ![img](https://www.zhihu.com/equation?tex=V_i) 是用户 u 的第 i 次互动行为的 Embedding 向量， ![img](https://www.zhihu.com/equation?tex=g%28V_i%2C+V_a%29) 为注意力得分，即候选广告与用户第 i 次行为的相似度。模型中注意力激活单元的设计是输入两个 Embedding 向量，经过元素减操作 (element-wise minus) 后，与原 Embedding 向量拼接后送入全连接层得出注意力分值。如下图右上角所示。

   - **DIEN**：2019 年阿里提出 DIEN(Deep Interest Envole Network) 在 DIN 的基础上融合了序列模型，用于模拟用户兴趣随时间的变化过程。在电商广告推荐场景中，序列信息加强了用户最近行为对下次行为预测的影响，能够学习用户的购买趋势。出于这样的目的，阿里巴巴在 DIN 的基础上引入了兴趣进化网络学习用户的兴趣 Embedding。模型结构图如下：

   DIEN 的兴趣进化网络分为三层：行为序列 (Behavior Layer) 层，把原始的 id 类行为序列转换成 Embedding 行为序列；兴趣抽取 (Interest Extractor Layer) 层，抽取用户兴趣，模拟用户兴趣迁移；兴趣进化 (Interest Evolving Layer) 层，增加注意力机制，模拟与目标广告相关的兴趣进化过程。其中兴趣抽取层的基本结构是 GRU(Gated Recurrent Unit)，将用户的行为向量 Embedding 转换成兴趣状态向量。兴趣进化层也是通过 AUGRU(GRU with Attentional Update gate)引入注意力机制的，在原 GRU 的更新门结构上增加了注意力得分。

   - **MIND**：DIEN 之后，阿里天猫团队又提出使用胶囊网络提取用户的多样兴趣，再加上基于标签的注意力机制，提升召回的丰富度和准确度，此模型并未包含在书中，但笔者认为 MIND 模型还是有很多值得探究的地方，也有实际落地的效果和意义。关于胶囊网络的理解感兴趣的小伙伴可自行阅读相关论文，此处只解释 MIND 模型中的用法。用户交互的物品 Embedding 集合作为多样兴趣提取 (Multi-interest Extractor Layer) 层的输入，经过胶囊网络的动态路由算法生成一组用户兴趣 Embedding(可以视为用户行为的软聚类)。用户基础画像属性的 Embedding 分别与用户兴趣 Embedding 拼接，经过两层全连接，得到一组用户 Embedding。再根据 label 的 Embedding 与用户 Embedding 求相似度作为注意力权重。MIND 网络结构如下图所示：
   
   最后是强化学习与推荐系统的融合：
   
     - **DRN**：强化学习相比传统深度模型的优势在于强化学习模型能够进行 “在线学习”，不断利用新学到的知识更新自己，及时调整和反馈。DRN(Deep Reinforcement Learning) 框架中起决定作用的是 DQN(Deep Q-Network), 对 DQN 感兴趣的小伙伴可以自行阅读相关论文，这里只说推荐系统中如何使用 DQN。DQN 把用户特征和环境特征归为和具体行动无关的状态向量，把用户 - 新闻交叉特征以及新闻特征归为与推荐内容这一行动相关的行动特征。DQN 的网络结构如下图所示。用户特征和环境特征经过左侧多层神经网络拟合得到价值得分 ![img](https://www.zhihu.com/equation?tex=V%28s%29) ，利用状态向量和行动向量生成优势得分 ![img](https://www.zhihu.com/equation?tex=A%28s%2Ca%29) , 综合二者分值，得到最终的质量得分 ![img](https://www.zhihu.com/equation?tex=Q%28s%2Ca%29) 。



