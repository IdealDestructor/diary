---
title: 论文阅读笔记之Long-Tailed Classification by Keeping the Good and Removing the Bad Momentum Causal Effect NIPS，2020 
date: 2021-05-13 14:22:35
tags: [深度学习,CV]
categories: 文献阅读
widgets: null
---

### 背景和动机

![img](https://pic3.zhimg.com/80/v2-3c2009cd25376e7bd63b40cee7aa3de6_1440w.jpg)

在传统的长尾分布处理方法中，普遍使用的重采样、重加权等re-balancing办法可能导致对头部类欠拟合且对尾部类过拟合，从而产生shortcut。而Decoupling为代表的二阶段训练方法则不太符合深度学习端到端的理念。
<div>
$$
v_{t}=\underbrace{\mu \cdot v_{t-1}}_{\text {momentum }}+g_{t}, \quad \theta_{t}=\theta_{t-1}-l r \cdot v_{t},
$$
</div>
通过分析优化器的动量项可能在训练数据时引入数据分布，于是试图通过因果分析去改进动量项，得出一种通用且有一定可解释性的长尾问题解决方法。

<!--more-->

### 核心思想

1. multi-head normalized classifier 
<div>
$$
Y_{i}=\frac{\tau}{K} \sum_{k=1}^{K}+\frac{\left(w_{i}^{k}\right)^{T} x^{k}}{\left(\left\|w_{i}^{k}\right\|+\gamma\right)\left\|x^{k}\right\|}
$$
</div>
其中<div>$$
\tau, \gamma
$$</div>是超参，K是multi-head的数量

2.统计一个移动平均特征<div>$$
\bar{x}
$$</div>，并将他的单位方向看作是特征对头部类的倾向方向<div>$$
\hat{d}=\bar{x} /\|\bar{x}\|$$</div>
3.从training的logits中剔除代表对头部类过度倾向的部分，即测试时改用如下公式计算TDE logits：
<div>$$
\operatorname{TDE}\left(Y_{i}\right)=\frac{\tau}{K} \sum_{k=1}^{K}\left(\frac{\left(w_{i}^{k}\right)^{T} x^{k}}{\left(\left\|w_{i}^{k}\right\|+\gamma\right)\left\|x^{k}\right\|}-\alpha \cdot \frac{\cos \left(x^{k}, \hat{d}^{k}\right) \cdot\left(w_{i}^{k}\right)^{T} \hat{d}^{k}}{\left\|w_{i}^{k}\right\|+\gamma}\right)
$$</div>
4.对background类做特殊处理
<div>$$
\underset{i \in C}{\arg \max }\left\{\begin{array}{ll}
\left(1-p_{0}\right) \cdot \frac{q_{i}}{1-q_{0}} & i \neq 0 \\
p_{0} & i=0
\end{array}\right.
$$</div>
​		background类是一个头部大类，我们可以依赖对background的bias来倚除大量琐碎的细节。公式中i=0代表background类, $\quad p_{i}$ 是利用原始 training的logits计算出的probability, $q_{i}$ 是利用TDE logits计算出的softmax后的概率。

### 理论依据

1.因果图
![img](https://pic4.zhimg.com/80/v2-50bf08ae63c68a6794cad924396547fb_1440w.jpg)

通常意义上，在因果推断理论中，M是对推断X->Y的混淆因子，D则是中介因子，它们都有可能对推理的正确性产生影响。

而在当前的问题中，M就是优化器的动量，X是backbone提取的核心特征，Y是预测。D是特征对头部大类的偏移量。

2.De-confound-TDE

![img](https://pic1.zhimg.com/80/v2-aeb0b5c18e021b302263ffd7e49587c4_1440w.jpg)

（1）使用De-confound training在训练中控制M对X的影响，但是因为无法统计M的真实分布，通过multi-head多重采样来近似。

（2）把原始的logits当成是X对Y的因果效应，根据propensity score（排除协变量的影响）思想，应该对大类和小类等所有类做归一化统一分布，将其实现为一种logits的normalization，其中包含类别相关与类别不相关两个normalization项。

（3）counterfactual inference做减法，通过安慰剂对照组，去除了间接效应。

3.不同策略分类效果对比

![img](https://pic4.zhimg.com/80/v2-dd61188a776237d5366c1ec8a27d733b_1440w.jpg)

根据预测分布，可以看出直接训练和one-stage的re-balancing都有严重的问题，而two-stage的方法通过再训练去矫正分类边界。TDE方法则直接矫正特征本身的分布，从而解决长尾问题。

### 实验结果

1.在mageNet-LT和Long-tailed CIFAR-10/-100上的表现

![img](https://pic2.zhimg.com/80/v2-b21ee69bfbb1b1b68b1d6dda1ecf25ad_1440w.jpg)

![img](https://pic3.zhimg.com/80/v2-e4bd51fd767952c91f5a85f8eab91352_1440w.jpg)

可以看出TDE方法在长尾数据集上表现较以往的算法有明显提升。

2.feature map可视化

![img](https://pic2.zhimg.com/80/v2-28e5f7c98a98e01782695b3c4c62bd3d_1440w.jpg)

发现De-confound-TDE使得feature map更加关注高区分度的特征。
