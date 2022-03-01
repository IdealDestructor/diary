---
title: Prompt综述 
date: 2022-03-01 14:22:35
tags: [深度学习]
categories: 论文阅读
widgets: null
password: 328
abstract: 这是一篇加密博文，请输入密码后查看
message: 这里需要密码才能访问。
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
---

个人觉得2021年NLP最火的两个idea，一个是[对比学习（Contrastive Learning）](https://wmathor.com/index.php/archives/1580/)，另一个就是Prompt

### 浅谈我对PROMPT的理解

Prompt说简单也简单，看了几篇论文以及博客后发现其实就是构建一个语言模版，从而实现无监督训练。但是细想起来又觉得复杂，因为总感觉里面还有很多细节，因此本文就来从头梳理一下Prompt（Prompt很多地方会翻译成「范式」，但是「范式」这个词本身也不好理解，因此读者把他看作是「模板」即可）

今天我还与室友讨论预训练模型（例如BERT）到底做了什么，我给出的回答是

> 预训练模型提供了一个非常好的初始化参数，这组参数在预训练任务上的表现非常好（预训练损失非常低），但是由于下游任务千奇百怪，我们需要在这组参数的基础上进行Fine-tune以适应我们的下游任务（使得下游任务的损失值非常低）

上面这段话其实隐含了目前做NLP任务的大致流程，即"Pre-train, Fine-tune"，而对我们来说实际上大部分时候都是直接拿别人预训练好的模型做Fine-tune，并没有Pre-train这一步

融入了Prompt的模式大致可以归纳成"Pre-train, Prompt, and Predict"，在该模式中，下游任务被重新调整成类似预训练任务的形式。例如，通常的预训练任务有MLM（Masked Language Model），在文本情感分类任务中，对于"I love this movie"这句输入，可以在后面加上Prompt：“the movie is ___”，组成如下这样一句话：

```shell
I love this movie, the movie is ___
```

然后让预训练模型用表示情感的答案（例如"great"、"terrible"等）做完形填空，最后再将该答案转换为情感分类的标签。这样一来，我们就可以通过构造合适的「模板」，控制模型的输出空间，从而训练一个完全无监督的预训练模型来解决各种各样的下游任务

> 注意，Prompt设计的这种完形填空和MLM任务是有区别的，二者虽然都是都是词分类，但是候选集不同，MLM的候选词是整个词库，不过如果是生成任务，那么Prompt和MLM的候选集就是一样的，都是整个词库

### 如何构建PROMPT

对于输入文本xx*x*，存在一个函数fPrompt(x)f_{\text{Prompt}}(x)*f*Prompt(*x*)，将xx*x*转化成x‘x^{‘}*x*‘的形式，即
$$
x^{’}=f_{\text{Prompt}}(x)
$$
该函数通常会进行两步操作：

1. 使用一个模板，模板通常为一段自然语言句子，并且该句子包含两个空位置：用于填输入xx*x*的位置[X][X][*X*]、用于生成答案文本zz*z*的位置[Z][Z][*Z*]
2. 把输入xx*x*填到[X][X][*X*]的位置

以前文提到的例子为例，在文本情感分类任务中，假设输入是

```python
x = "I love this movie"
```

使用的模板是

```shell
[X]. Overall, it was a [Z] movie
```

那么得到的x′x^{'}*x*′就应该是

```shell
I love this movie. Overall, it was a [Z] movie
```

在实际情况中，Prompt来填充答案的位置一般在句中或句末。如果在句中，一般称这种Prompt为**Cloze Prompt**；如果在句末，一般称这种Prompt为**Prefix Prompt**。[X][X][*X*]和[Z][Z][*Z*]的位置、数量以及使用模板句的不同，都有可能对结果造成影响，因此需要灵活调整

上面讲的都是简单的情感分类任务的Prompt设计，读者看到这里自然而然的会想到，其他NLP任务的Prompt如何设计呢？实际上刘鹏飞大神在他的[论文](https://arxiv.org/abs/2107.13586)中给我们提供了一些参考

[![img](https://z3.ax1x.com/2021/10/24/5W7pnA.png#shadow)](https://z3.ax1x.com/2021/10/24/5W7pnA.png#shadow)

> Text Generation中摘要任务里有一个关键字`TL;DR`，这其实是`Too Long; Don't Read`的缩写

### PROMPT的选择非常重要且困难

有上述Prompt的基础后，我们可以得知Prompt的设计主要包含两部分：

1. **模板T**：例如`[X]. Overall, It was [Z]`
2. **标签词映射**：即[Z][Z][*Z*]位置预测输出的词汇集合与真实标签yy*y*构成的映射关系。例如，标签positive对应单词great，标签negative对应单词terrible

在基于Prompt的微调方法中，不同的模板和标签词对最终结果影响很大，下图是陈丹琦团队[论文](https://arxiv.org/abs/2012.15723)中的实验结果

[![img](https://z3.ax1x.com/2021/10/24/5WbFSS.jpg#shadow)](https://z3.ax1x.com/2021/10/24/5WbFSS.jpg#shadow)

从上图我们可以看出两点：

1. 使用相同的「模板」，不同的「标签词」会产生不一样的效果。例如`great/terribel`和`cat/dog`这两组标签词的效果不一样，而且即便是相同标签词，互换顺序也会导致最终效果有所变化，例如`cat/dog`和`dot/cat`
2. 使用相同「标签词」，对「模板」进行小改动（例如增删标点）也会呈现不同的结果

### PROMPT的设计

Prompt大概可以从下面三个角度进行设计：

- Prompt的形状
- 人工设计模板
- 自动学习模板

#### Prompt的形状

Prompt的形状主要指的是[X][X][*X*]和[Z][Z][*Z*]的位置和数量。上文提到的**Cloze Prompt**与Maksed Language Model的训练方式非常类似，因此对于MLM任务来说，Cloze Prompt更合适；对于生成任务或者使用自回归LM解决的任务，**Prefix Prompt**更合适

#### 人工设计模板

Prompt的模板最开始是人工设计的，人工设计一般基于人类的自然语言知识，力求得到语义流畅且高效的「模板」。例如，Petroni等人在著名的[LAMA数据集](https://github.com/facebookresearch/LAMA)中为知识探针任务人工设计了Cloze Templates；[Brown等人](https://arxiv.org/abs/2005.14165)为问答、翻译和探针等任务设计了Prefix Templates。人工设计模板的优点是直观，但缺点是需要很多实验、经验以及语言专业知识。下图是[GPT Understands, Too](https://arxiv.org/abs/2103.10385)论文中的一个实验结果

[![img](https://z3.ax1x.com/2021/10/25/5hp3Wj.png#shadow)](https://z3.ax1x.com/2021/10/25/5hp3Wj.png#shadow)

可以看到不同的Prompt只有细微的区别，有的甚至只是增加减少一个词，但是最后的结果会差几十个点

#### 自动学习模板

为了解决人工设计模板的缺点，许多研究员开始探究如何自动学习到合适的模板。自动学习的模板又可以分为离散（Discrete Prompts）和连续（Continuous Prompts）两大类。离散方法主要包括：[Prompt Mining](https://arxiv.org/abs/1911.12543)，Prompt Paraphrasing，[Gradient-based Search](https://arxiv.org/abs/2010.15980)，[Prompt Generation](https://arxiv.org/abs/2012.15723)和[Prompt Scoring](https://arxiv.org/abs/1909.00505)；连续的则主要包括[Prefix Tuning](https://arxiv.org/abs/2101.00190)，[Tuning Initialized with Discrete prompts](https://arxiv.org/abs/2104.05240)，[Hard-Soft Prompt Hybrid Tuning](https://arxiv.org/abs/2103.10385)，[P-Tuning v2](https://arxiv.org/abs/2110.07602)

#### 离散Prompts

简单说一下上述几种方法，首先是离散的[Prompt Mining](https://arxiv.org/abs/1911.12543)，这篇文章发表在TACL 2020，讲的是如何拿预训练语言模型当作「知识库」使用，并且引入了依存树和Paraphrase（转述）等方法来挖掘更好的「模板」，下图是实验结果

[![img](https://z3.ax1x.com/2021/10/25/5hiZ4S.png#shadow)](https://z3.ax1x.com/2021/10/25/5hiZ4S.png#shadow)

可以看到，被挖掘出来的若干「连接谓词」相比于人工设计的「模板」结果提升还是很明显的

有很多种方法可以实现Prompt Paraphrsing，例如「回译」，我们通过DeepL翻译看个例子：

[![img](https://z3.ax1x.com/2021/10/25/5hV6cF.png)](https://z3.ax1x.com/2021/10/25/5hV6cF.png)

[![img](https://z3.ax1x.com/2021/10/25/5hV5h6.png)](https://z3.ax1x.com/2021/10/25/5hV5h6.png)

这样我们就得到了`x shares a border with y`的一个Prompt Paraphrasing：`x and y share a boundary`

论文[BARTScore](https://arxiv.org/abs/2106.11520)干脆给我们提供了一张表，里面有各种词组的同义替换，这个我再熟悉不过了，因为以前英语考试我也背过类似的东西

[![img](https://z3.ax1x.com/2021/10/25/5hKJ10.png#shadow)](https://z3.ax1x.com/2021/10/25/5hKJ10.png#shadow)

[Gradient-based Search](https://arxiv.org/abs/2010.15980)（基于梯度的搜索）是由论文AUTOPROMPT提出的，这篇文章发表在EMNLP 2020，它的主要思想用下面这张图就可以表示

[![img](https://z3.ax1x.com/2021/10/25/5hMXM6.png#shadow)](https://z3.ax1x.com/2021/10/25/5hMXM6.png#shadow)

上图中，`a real joy`是原始的输入句子xinpx_{\text{inp}}*x*inp，红色的Trigger tokens是由xinpx_{\text{inp}}*x*inp「激发」的相关词汇集合xtrigx_{\text{trig}}*x*trig，根据Template λ\lambda*λ*的配置，将xtrigx_{\text{trig}}*x*trig和xinpx_{\text{inp}}*x*inp组合起来构造最终的输入xpromptx_{\text{prompt}}*x*prompt，送入Masked LM预测情感标签。下面的表格增加了很多NLP其他任务的例子

[![img](https://z3.ax1x.com/2021/10/25/5h8Hvn.png#shadow)](https://z3.ax1x.com/2021/10/25/5h8Hvn.png#shadow)

> 关于如何生成xtrigx_{\text{trig}}*x*trig集合，实际上主要使用的是HotFlip和对抗训练的思想，感兴趣的同学可以看原论文以及[HotFlip: White-box adversarial examples for text classification](https://arxiv.org/abs/1712.06751)、[Universal Adversarial Triggers for Attacking and Analyzing NLP](https://arxiv.org/abs/1908.07125)这两篇论文

[Prompt Generation](https://arxiv.org/abs/2012.15723)是陈丹琦团队的一项工作，主要是把Seq2Seq预训练模型T5应用到模板搜索的过程。T5基于多种无监督目标进行预训练，其中最有效的一个无监督目标就是：利用<X>或<Y>替换一个或多个连续span，然后生成对应输出。例如：

```shell
Thank you <X> me to your party <Y> week
```

T5会在<X>生成`for inviting`，在<Y>生成`last`。很显然，T5这种方式很适合生成模板，而且不需要指定模板的token数。具体来说，有三种可能的生成方式
$$
\begin{align}
\langle S_1\rangle &\to \langle \text{X}\rangle \ \mathcal{M}(y) \ \langle \text{Y} \rangle\ \langle S_1\rangle\
\langle S_1\rangle &\to \langle S_1\rangle\ \langle \text{X}\rangle \ \mathcal{M}(y) \ \langle \text{Y} \rangle\
\langle S_1\rangle ,\langle S_2 \rangle &\to \langle S_1\rangle\ \langle \text{X}\rangle \ \mathcal{M}(y) \ \langle \text{Y} \rangle\ \langle S_2\rangle\
\end{align}
$$
具体的模板生成过程如下图所示：

[![img](https://z3.ax1x.com/2021/10/25/54P82Q.png#shadow)](https://z3.ax1x.com/2021/10/25/54P82Q.png#shadow)

首先在标签词前后添加填充位<X>和<Y>（上面提到的三种生成方式），然后将其送入T5模型中，T5会自动在填充位生成序列，最后将标签词（great或terribel）转换为[MASK]标签，形成多个模板。具体过程中采用Beam Search的方法生成多个候选模板，然后对每一个候选模板利用dev集进行微调，选择其中一个最佳模板

我还想说一下这篇论文中另外一个有意思的点，最后送入模型进行预测的句子还拼接上了每种类别的「示例」（Demonstration），如下图所示

[![img](https://z3.ax1x.com/2021/10/25/54kMUx.png#shadow)](https://z3.ax1x.com/2021/10/25/54kMUx.png#shadow)

这种Prompt的设计有点像是在做语义相似度任务，XX*X*为原始Input句子，已知YY*Y*为正例，ZZ*Z*为负例，构造了如下形式的输入：

```shell
X是[MASK]例？Y为正例；Z为负例
```

这有点像是编程语言中的三目运算符，或者说相当于让模型比较XX*X*与YY*Y*、ZZ*Z*的语义相似度。这里我们自然而然会想问：YY*Y*、ZZ*Z*是如何挑选出来的？实际上是依据下面两条规则：

1. 对于每个原始输入句子，从每个类别中随机采样一个样本「示例」拼接到Prompt中
2. 对于每个原始输入句子，在每个类别中，**通过与Sentence-BERT进行相似度计算，从相似度最高的前50%样本中随机选择一个样本「示例」**

#### 连续Prompts

构造Prompt的初衷是能够找到一个合适的方法，让Pre-trained Language Model（PLM）更好地输出我们想要的结果，但其实并不一定要将Prompt的形式设计成人类可以理解的自然语言，只要机器理解就行了。因此，还有一些方法探索连续型Prompts——直接作用到模型的Embedding空间。连续型Prompts去掉了两个约束条件：

1. 模版中词语的Embedding可以是整个自然语言的Embedding，不再只是有限的一些Embedding
2. 模版的参数不再直接取PLM的参数，而是有自己独立的参数，可以通过下游任务的训练数据进行调整

[Prefix Tuning](https://arxiv.org/abs/2101.00190)最开始由Li等人提出，这是一种在输入句子前添加一组连续型向量的方法，该方法保持PLM的参数不动，仅训练前缀（Prefix）向量。Prefix Tuning的提出主要是为了做生成任务，因此它根据不同的模型结构定义了不同的Prompt拼接方式，在GPT类的Auto-Regressive（自回归）模型上采用的是[Prefix;x;y][\text{Prefix}; x; y][Prefix;*x*;*y*]的方式，在T5类的Encoder-Decoder模型上采用的是[Prefix;x;Prefix′;y][\text{Prefix};x;\text{Prefix}';y][Prefix;*x*;Prefix′;*y*]的方式

[![img](https://z3.ax1x.com/2021/10/26/55TW6O.png#shadow)](https://z3.ax1x.com/2021/10/26/55TW6O.png#shadow)

输入部分Prefix,x,y\text{Prefix},x,yPrefix,*x*,*y*的Position id分别记作P<em>idx,X</em>idx,Y<em>idx\text{P}<em>{\text{idx}},\text{X}</em>{\text{idx}},\text{Y}<em>{\text{idx}}P<*e**m*>idx,X</*e**m*>idx,Y<*e**m*>idx。Prefix Tuning初始化一个可训练的矩阵，记作P</em>θ∈R∣Pidx∣×dim⁡(hi)P</em>\theta \in \mathbb{R}^{\lvert P_{\text{idx}}\rvert \times \dim (h_i)}*P*</*e**m*>*θ*∈R∣*P*idx∣×dim(*h**i*)，其中
$$
h_i = \begin{cases}P_\theta [i,:],\quad &\text{if}\ i \in \text{P}*{\text{idx}}\
\mathbf{LM}*{\phi}(z_i,h_{<i}), \quad &\text{otherwise}
\end{cases}
$$
上述公式的含义是，索引ii*i*如果属于前缀的部分，则从PθP_\theta*P**θ*​中抽取向量；ii*i*如果不是前缀部分，则由**参数固定的预训练模型**生成对应的向量。训练目标为：
$$
\mathop{\text{max}}\limits_{\phi} \ \log p_{\phi}(y\mid x) = \sum\limits_{i\in \text{Y}*{\text{idx}}} \log p*{\phi} (z_i\mid h_{<i})
$$

> PθP_{\theta}*P**θ*本质上是一个矩阵，而生成一个矩阵的方法又很多，可以用`nn.Embedding()`，或者`nn.Linear()`

同样是在连续空间上搜索Prompt，[OptiPrompt](https://arxiv.org/abs/2104.05240)构建的「模板」并不局限于前缀，也可以在句子的中间

[![img](https://z3.ax1x.com/2021/10/26/55jpsP.png#shadow)](https://z3.ax1x.com/2021/10/26/55jpsP.png#shadow)

首先根据AutoPrompt定义一个Prompt模板：
$$
[x]\ [v]_1\ [v]_2\ …\ [v]_m\ [\text{MASK}]
$$
其中[v]i[v]_i[*v*]*i*​为一个连续型向量（与BERT的输入维度一致）。OptiPrompt还考虑以人工构建的离散Prompt作为起点，在连续空间上进行搜索以构建较优的Prompt。例如[x] is [MASK] citizen[x]\ \text{is}\ [\text{MASK}]\ \text{citizen}[*x*] is [MASK] citizen可以转换为
$$
[x]\ [v]_1\ [\text{MASK}]\ [v]_2
$$
将`is`和`citizen`对应的input Embedding作为[v]1[v]_1[*v*]1​和[v]2[v]_2[*v*]2​的初始化

Hard-Soft Prompt Hybrid Tuning方法可以说是人工设计和自动学习的结合，它通常不单纯使用可学习的Prompt模板，而是在人工设计的模板中插入一些可学习的Embedding。实际上有了上面的基础我们都知道，连续的Prompt要比离散的Prompt好一点，但是在此基础上还有什么改进的余地吗？Liu等人提出的**[P-Tuning](https://arxiv.org/abs/2103.10385)**解决了Prompt token之间的关联性问题

之前连续的Prompt生成方式无非都是训练一个矩阵，然后通过索引出矩阵的某几行向量拼起来。坦白地说，我们希望这些prompt token Embedding之间有一个比较好的关联性，而不是独立地学习，为了解决这个问题，P-Tuning引入了一个Prompt Encoder（如下图b所示）

[![img](https://z3.ax1x.com/2021/10/26/5ICmh8.png#shadow)](https://z3.ax1x.com/2021/10/26/5ICmh8.png#shadow)

上图a是传统的离散型Prompt，我们把生成离散Prompt token的东西叫做Prompt Generator；上图b首先传入一些Virtual（Pseudo）token，例如BERT词表中的[unused1],[unused2],…当然，这里的token数目是一个超参数，插入的位置也可以调整。将这些Pseudo token通过一个Prompt Encoder得到连续的向量h0,…,hmh_0,…,h_m*h*0,…,*h**m*，其中
$$
\begin{align}
h_i &= \text{MLP}([\overrightarrow{\mathop{h_i}};\overleftarrow{\mathop{h_i}}])\
&= \text{MLP}([\text{LSTM}(h_{0:i}):\text{LSTM}(h_{i:m})])
\end{align}
$$
即，Prompt Encoder是由BiLSTM+MLP组成的一个简单网络。作者还发现加入一些anchor token（领域或者任务相关的token）可以有助于Template的优化。例如文本蕴含任务，输入是前提和假设，判断是否蕴含。一个连续的模版是
$$
\text{[PRE]} [\text{continuous tokens}][\text{HYP}][\text{continuous tokens}] [\text{MASK}]
$$
在其中加入一个anchor token：`[?]`效果会更好，此时模板变成
$$
\text{[PRE]} [\text{continuous tokens}][\text{HYP}]?[\text{continuous tokens}] [\text{MASK}]
$$
大家可能想问，如何优化P-tuning？实际上根据标注数据量的多少，分两种情况讨论

1. **标注数据比较少**。这种情况，我们固定PLM的参数，只优化[P0]∼[Pm][\text{P}_0]\sim [\text{P}_m][P0]∼[P*m*]这几个token的Embedding。换句话说，我们只是要更新Prompt Encoder的参数
2. **标注数据很充足**。这种情况直接放开所有参数微调

就在P-Tuning方法提出不久后，Liu等人又提出了[P-Tuning v2](https://arxiv.org/abs/2110.07602)，主要解决P-Tuning的两个问题：

1. 当预训练模型的参数量低于100亿（10B）时，Prompt tuning会比传统的Fine-tuning差
2. 诸如序列标注这样对推理和理解要求高的任务，prompt tuning效果会变差

Liu等人认为先前的P-Tuning只用了一层BiLSTM来编码Pseudo token，这是其推理能力不足的原因之一，因此v2版本提出Deep Prompt Tuning，用Prefix Tuning中的深层模型替换BiLSTM，如下图所示

[![img](https://z3.ax1x.com/2021/10/26/5opv3F.png#shadow)](https://z3.ax1x.com/2021/10/26/5opv3F.png#shadow)

P-Tuning v2相比于P-Tuning，区别在于：

- **取消Reparameterization**：以前的方法利用重参数化功能来提高训练速度和鲁棒性（例如，用于Prefix-Tuning的MLP和用于P-Tuning的LSTM）。在P-Tuning v2中，作者发现重参数化的改进很小，尤其是对于较小的模型，同时还会影响模型的表现
- **Multi-task Learning**：Deep Prompt Tuning的优化难题可以通过增加额外的任务数据或者无标注数据来缓解，同时可微调的Prefix Continuous Prompt也可以用来做跨任务的知识共享。例如在NER中，可以同时训练多个数据集，不同数据集使用不同的顶层Classifier，但是Prefix Continuous Prompt是共享的
- **取消verbalizer**：v2取消了标签映射，完全变为生成模型，可以在[CLS]部分输出句子级别的标签（Sentence-level label），也可以在每个token位置输出token级别的标签（Token-level label），直接输出真实标签

关于P-Tuning还有一些碎碎念，主要是从各个博客上看到的，汇总在这里。首先是v1版本的LSTM，实际上引入LSTM目的是为了帮助「模板」生成的token（某种程度上）更贴近自然语言，或者说token之间的语义更流畅，但更自然的方法应该是在训练下游任务的时候，不仅预测下游任务的目标token（例如"great"、“terrible”），**还应该同时做其他token的预测**

比如，如果是MLM模型，那么也随机MASK掉其它的一些token来预测，如果是LM模型，则预测完整的序列，而不单单是目标词。这样做的理由是：因为我们的MLM/LM都是经过自然语言预训练的，所以我们认为它能够很好的完成序列的重构，即便一开始不能，随着迭代轮数的增加，模型也能很好完成这项任务。所以这本质上是让模型进行「负重训练」

### *为什么要引入PROMPT？

[![img](https://z3.ax1x.com/2021/10/24/5W4pgs.png#shadow)](https://z3.ax1x.com/2021/10/24/5W4pgs.png#shadow)

在标准的Fine-tune过程中（如上图b所示），新引入的参数量可能会很大（独立于原始预训练模型外的参数），例如基于RoBERTa-large的二分类任务会新引入2048个参数（`nn.Linear(1024, 2)`），如果你仅有例如64个标注数据这样的小样本数据集，微调会非常困难

为解决这一问题，Prompt应运而生（如上图a所示），直接将下游任务转换为输出空间有限的MLM任务。值得注意的是：上述方法在预训练参数的基础上进行微调，并且没有引入任何新参数，同时还减少了微调和预训练任务之间的差距。总的来说，这可以更有效地用于小样本场景

### PROMPT的挑战与展望

尽管Prompt研究搞得如火如荼，但目前仍存在许多问题值得研究者们去探究

1. **Prompt的设计问题**。目前使用Prompt的工作大多集中于分类任务和生成任务，其它任务则较少。另外，「模板」和「答案」的联系也亟待解决。模型的表现同时依赖于使用的「模板」和「答案」的映射，如何同时搜索或者学习出两者联合的最好效果仍然很具挑战性
2. **Prompt的理论分析和可解释性**。尽管Prompt方法在很多情况下都取得了成功，但是目前Prompt-based Learning理论分析还很少，人们很难了解Prompt为什么能达到好的效果，又为什么在自然语言中意义相近的Prompt有时效果却相差很大
3. **Prompt在PLM debias方面的应用**。由于PLM在预训练过程中见过了大量的人类世界的自然语言，所以很自然地会受到一些影响。举一个简单的例子，比如说训练语料中有非常多"The capital of China is Beijing"，导致模型每次看到"capital"的时候都会预测出"Beijing"，而不是去分析到底是哪个国家的首都。在应用的过程中，Prompt还暴露了PLM学习到的很多其它bias，比如种族歧视、性别对立等。这也许会是一个值得研究的方向

### ONE MORE THING

最后我还想提一个实际Code过程中存在的问题。我们知道MLM任务会输出句子中[MASK]位置最有可能的词，而Prompt也类似的，例如下面的例子

```
这是一条__新闻。中国足球出线的可能性只有0.001%，留给中国队的时间不多了
```

这是一个新闻分类问题，真实标签有"体育"、“财经”、“娱乐"等，上面的样本很明显是一条体育新闻，因此我们希望模型对[MASK]部分输出"体育”，但事实真的如此吗？实际情况模型的输出可能是"足球"，但你认为模型预测的"足球"有问题吗？好像也没啥毛病，因此这就引申出了Prompt的一个问题，是否应该限制模型的输出空间？

还是上面新闻分类的例子，我们是否应该限制模型输出的空间，让他固定只能预测"体育"、“财经”、"娱乐"这几个标签？或者我们干脆把这几个标签换成索引，那就是让模型从0，1，2这三个数字选一个。Wait Wait Wait，如果这么做的话，和Fine-Tune有什么区别，Fine-Tune也是把标签转换成索引，让模型看了句子之后，从这几个索引中选一个作为预测值

这么说的话，那我们就不应该限制模型的输出空间，可是这样的话[MASK]位置的输出就限制的太死了，必须一定是"good"、“财经"才算对，如果输出"nice”、“财政"就算错。实际上输出近义词或者相似词，在零样本的情况下会经常出现，但是如果你用一些有标签的样本去训练，模型自己就会慢慢固定输出空间。例如"财经”，它不会预测成"财政"，只会预测成其它类型的新闻，例如"体育"

### REFERENCES

- [P-tuning：自动构建模版，释放语言模型潜能](https://kexue.fm/archives/8295)
- [必须要GPT3吗？不，BERT的MLM模型也能小样本学习](https://kexue.fm/archives/7764)
- [NLP新宠——浅谈Prompt的前世今生](https://zhuanlan.zhihu.com/p/399295895)
- [超大规模新型预训练模型详解：少样本学习等近十个数据集取得第一](https://zhuanlan.zhihu.com/p/359433351)
- [GPT-3的最强落地方式？陈丹琦提出小样本微调框架LM-BFF，比普通微调提升11%～](https://zhuanlan.zhihu.com/p/341609647)
- [基于Prompt的MLM文本分类](https://blog.csdn.net/u013546508/article/details/115358833)
- [Prompt-based Language Models：模版增强语言模型小结](https://blog.csdn.net/c9Yv2cf9I06K2A9E/article/details/117914901)
- [鹏飞大神的Pre-train, Prompt, and Predict](https://zhuanlan.zhihu.com/p/396098543)
- [P-tuning：用“连续提示微调”来增强“超大规模语言模型”的下游能力](https://zhuanlan.zhihu.com/p/391992466)
- [Prompting: Better Ways of Using Language Models for NLP Tasks](https://thegradient.pub/prompting/)
- [Prompt Based Task Reformulation in NLP 调研](https://thinkwee.top/2021/05/13/pet/)
- [PromptPapers](https://github.com/thunlp/PromptPapers)