---
title: 逻辑回归推导及求导
date: 2022-02-25 14:22:35
tags: [机器学习]
categories: 人工智能
widgets: null
password: 328
abstract: 这是一篇加密博文，请输入密码后查看
message: 这里需要密码才能访问。
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
---

回归问题中，最简单的模型是线性回归。线性回归假设数据集的样本数据![](https://math.jianshu.com/math?formula=x_i%3D(x_{i1}%2Cx_{i2}%2C...%2Cx_{id}))与输出值![](https://math.jianshu.com/math?formula=y_i)之间近似满足一个线性关系。本文探讨的**逻辑回归**（logistics regression，LR)是在线性回归的基础上进行一点延伸，解决分类问题的模型。

> 这篇文章回答了以下问题：逻辑回归的表达形式是什么？损失函数是什么？如何进行推导（梯度下降）来得到参数值？
>
> 一、LR 与线性回归的联系
> =============
>
> 一般的，线性回归模型的表达式可以写作：
>
> ![](https://math.jianshu.com/math?formula=y%3D\omega ^Tx%2Bb\\)
>
> 但许多问题不一定是线性的，![](https://math.jianshu.com/math?formula=\omega ^Tx%2Bb)的值可能和实际的标记值有一定的关系。更一般的，作为对线性回归的推广，广义线性模型的一般表达式为：
>
> ![](https://math.jianshu.com/math?formula=y%3Dg^{-1}(\omega ^Tx%2Bb)\\)
>
> 其中，![](https://math.jianshu.com/math?formula=g(·))是单调可微函数。逻辑回归模型就是广义线性模型的一种。
>
> 对于分类任务，原先用于线性回归的表达式得到的结果是一个连续的实值，那怎样转换为分类任务呢？最简单的方法是设定一个阈值，超过这个阈值视为正例，小于这个阈值视为负例。这种做法可以看做引入了一个 “单位阶跃函数”。这种方法有个明显的缺点，那就是引入阈值或引入阶跃函数是不可导的，数学性质很差。第二个方法对前面方法进行了改进，使用 sigmoid 函数代替阈值法，众所周知 sigmoid 函数取值范围为（0,1）且当 x=0 时变化较大。**sigmoid 函数表达式**写作：
>
> ![](https://math.jianshu.com/math?formula=y%3D\frac{1}{1%2Be^{-z}} \\)
>
> 将 sigmoid 与线性回归函数结合，即为逻辑回归。简言之，**逻辑回归 = 线性回归 + sigmoid**。因此，**逻辑回归的表达式**为：
>
> ![](https://math.jianshu.com/math?formula=y%3D\frac{1}{1%2Be^{-(\omega ^Tx%2Bb)} } \\)
>
> 对上式进行变化可得：
>
> ![](https://math.jianshu.com/math?formula=ln \frac{y}{1-y} %3D \omega ^T x%2Bb \\)
>
> 如果把![](https://math.jianshu.com/math?formula=y)视为![](https://math.jianshu.com/math?formula=x)为正例的可能性，那么![](https://math.jianshu.com/math?formula=1-y)就是![](https://math.jianshu.com/math?formula=x)为负例的可能性。二者的比值的实际意义是![](https://math.jianshu.com/math?formula=x)作为正例的 “相对可能性”，称为 “对数几率”。所以逻辑回归的本质其实是**利用线性回归去逼近真实标记的对数几率**。逻辑回归**对样本数据的假设是服从伯努利分布**。
>
> 二、损失函数推导
> ========
>
> 下面我们一步步进行推导求参数。按前面的思路，把 y 视为正例，即![](https://math.jianshu.com/math?formula=y %3D p(y%3D1|x))，![](https://math.jianshu.com/math?formula=1-y%3Dp(y%3D0|x))。这里引入变量![](https://math.jianshu.com/math?formula=\beta %3D(\omega %3Bb)%2C \hat{x} %3D(x%2C1))，目的是使得![](https://math.jianshu.com/math?formula=\omega ^Tx%2Bb%3D\beta \hat{x} )，方便讨论。可以解得：
>
> $$
> \begin{array}{l}
> p(y=1 \mid x)=\frac{e^{\omega^{T} x+b}}{1+e^{\omega^{T} x+b}}=\frac{e^{\beta \widehat{x}}}{1+e^{\beta \widehat{x}}} \\
> p(y=0 \mid x)=\frac{1}{1+e^{\omega^{T} x+b}}=\frac{1}{1+e^{\beta \widehat{x}}}
> \end{array}
> $$
> 要求出参数![](https://math.jianshu.com/math?formula=(\omega %2Cb))，采用对数最大似然方法，令![](https://math.jianshu.com/math?formula=p_1%3Dp(y%3D1|x)%2Cp_0%3Dp(y%3D0|x))，**损失函数**写作：
>
> ![](https://math.jianshu.com/math?formula=l(\beta )%3Dlog\coprod\nolimits_{i%3D1}^m (p_1)^{y_1}(1-p_1)^{1-y_1}%3D\sum_{i%3D1}^m (y_1logp_1%2B(1-y_1)log(1-p_1)))
>
> 结合![](https://math.jianshu.com/math?formula=p_i)的定义，推导得：  
> ![](https://math.jianshu.com/math?formula=l(\beta )%3D\sum_{i%3D1}^m y_i\beta \hat{x} -log(1%2Be^{\beta \hat{x} }) \\)
>
> 三、参数更新公式推导
> ==========
>
> 接下来，要去求![](https://math.jianshu.com/math?formula=(\omega %2Cb)%3D arg \max_{(\omega %2Cb)} l(\beta ) )。问题变成了**求以对数似然函数最大化为目标的优化问题**，采用梯度下降（上升）或拟牛顿法解决。关于梯度下降（上升）方法，这里是求最大值，对应的是梯度上升，一般常见的是梯度下降法使损失函数最小化，所以我们在这里加一个负号，要求的梯度可以写作：
>
> $$
> \begin{aligned}
> J(\beta) &=-\frac{1}{m} l(\beta) \\
> \frac{\nabla}{\nabla \beta} J(\beta) &=-\frac{1}{m} \frac{\nabla}{\nabla \beta} l(\beta)
> \end{aligned}
> $$
> 我们把损失函数的初始写法带入，推导：
>
> ![](https://math.jianshu.com/math?formula=-\frac{1}{m}  \frac{\nabla}{\nabla \beta } l(\beta )%3D-\frac{1}{m} \sum_{i%3D1}^m y_i \frac{1}{p_i} \frac{\nabla}{\nabla \beta }p_i-(1-y_i)\frac{1}{1-p_i} \frac{\nabla}{\nabla \beta } p_i\\ )
>
> 由于![](https://math.jianshu.com/math?formula=p_i%3Dp(y%3D1|x)%3D\frac{1}{1%2Be^{-\beta \hat{x} }} %3Dsig(\beta \hat{x} ))是一个 sigmoid 函数，所以它的导数![](https://math.jianshu.com/math?formula=sig(x){’}%3Dsig(x)*(1-sig(x)))。接着推导：
> $$
> \begin{array}{c}
> -\frac{1}{m} \frac{\nabla}{\nabla \beta} l(\beta)=-\frac{1}{m} \sum_{i=1}^{m}\left(y_{i} \frac{1}{\operatorname{sig}(\beta \widehat{x})}-\left(1-y_{i}\right) \frac{1}{1-\operatorname{sig}(\beta \widehat{x})}\right) \operatorname{sig}(\beta \widehat{x})(1-\operatorname{sig}(\beta \widehat{x})) \frac{\nabla}{\nabla \beta}(\beta \widehat{x}) \\
> =-\frac{1}{m} \sum_{i=1}^{m}\left(y_{i}(1-\operatorname{sig}(\beta \widehat{x}))-\left(1-y_{i}\right) \operatorname{sig}(\beta \widehat{x})\right) x \\
> =\frac{1}{m} \sum_{i=1}^{m}\left(\operatorname{sig}(\beta \widehat{x})-y_{i}\right) x \\
> =\frac{1}{m} \sum_{i=1}^{m}\left(p_{i}-y_{i}\right) x
> \end{array}
> $$
> 整理一下，也就是：
>
> ![](https://math.jianshu.com/math?formula=  \frac{\nabla}{\nabla \beta }J(\beta)%3D\frac{1}{m}\sum_{i%3D1}^m ( p_i-y_i)x \\ )
>
> 所以参数更新公式为：
>
> ![](https://math.jianshu.com/math?formula=\beta %3D\beta -\alpha *   \frac{\nabla}{\nabla \beta }J(\beta)\\)
>
> 这就是梯度下降法求参数的更新公式。
>
> 