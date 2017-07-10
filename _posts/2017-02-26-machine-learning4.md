---
layout: post
title: 机器学习与深度学习入门（四）
author: Ricardo
tags:	matlab machine_learning
---

> 这节进入逻辑回归。逻辑回归实际上是一个分类问题，很多情况下，我们的输出值y并不是一个连续量，很可能只是0、1这样的离散量。我们可能用1表示一种输出类型，用0表示另一种输出类型，显然此时不能像之前那样直接利用线性回归来判定我们的数据趋势。

<h4>Sigmoid Function</h4>
在这种情况下，我们先在数学上表示y的范围：
![](http://latex.codecogs.com/png.latex?y\in \{0,1\})
其次：
![](http://latex.codecogs.com/png.latex?0\le h_{\theta}(x)\le1)

我们将认为大于0.5的值认定为逻辑1，小于0.5的值认定为逻辑0，因此我们需要找到一个函数满足上面的关系，在这里我们使用sigmoid function，它同时也被称为logistic function：
![](http://latex.codecogs.com/png.latex?g(z)=\frac{1}{1+e^{-z}})
![](http://latex.codecogs.com/png.latex?h_{\theta}(x)=g(\theta^T X))

sigmoid函数的图像为：
![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Logistic-curve.svg/600px-Logistic-curve.svg.png)

我们可以看到当z大于0时，g(z)>0.5,z小于0时，g(z)<0.5.
即：
![\theta^T X \ge 0](http://upload-images.jianshu.io/upload_images/3205350-e93c69a21105dfaa.latex?imageMogr2/auto-orient/strip)

![](http://latex.codecogs.com/png.latex?\Downarrow)
![](http://latex.codecogs.com/png.latex?h_{\theta}=g(\theta^T X)\ge 0.5)
![](http://latex.codecogs.com/png.latex?\Downarrow)
![](http://upload-images.jianshu.io/upload_images/3205350-97f4974577e2b8b3.latex?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实际上h(x)的数值代表的是输出值为1的概率，就像上面这个推导式，h(x)≥0.5的意思指的是y=1的概率大于百分之五十，用数学表达式表示则为：
![](http://latex.codecogs.com/png.latex?h_{\theta}(x)=P(y=1|x;\theta)=1-P(y=0|x;\theta))

#### Cost Function
> 在线性回归中，cost function的表达式为：
> ![](http://latex.codecogs.com/gif.latex?J%28%5Ctheta%29%3D%5Cfrac%7B1%7D%7B2m%7D%5Csum_%7Bi%3D1%7D%5E%7Bm%7D%5Bh_%7B%5Ctheta%7D%28x%5E%7B%28i%29%7D%29-y%5E%7B%28i%29%7D%5D%5E2)
> 显然对于分类问题，这样的cost function是不合理的，因此我们可以构造一个函数来合理地描述分类问题的偏差值。

令：
![](http://latex.codecogs.com/png.latex?J(\theta)=\frac{1}{m}\sum_{i=1}^{m}Cost[h_{\theta}(x^{i}),y^{(i)}])
同时假设：
![](http://upload-images.jianshu.io/upload_images/3205350-9153650e332e8e39.latex?imageMogr2/auto-orient/strip)

根据上面的条件，我们可以构造一个函数，如下所示：
![](http://upload-images.jianshu.io/upload_images/3205350-cb1703c38d6c68da.latex?imageMogr2/auto-orient/strip)

为了计算方便，我们将上面的式子简化为：
![](http://upload-images.jianshu.io/upload_images/3205350-4b7e2345eb7547ba.latex?imageMogr2/auto-orient/strip)

所以逻辑回归的cost function为：
![](http://upload-images.jianshu.io/upload_images/3205350-f86e64fab500964c.latex?imageMogr2/auto-orient/strip)
将上面的式子向量化后得到：
![](http://latex.codecogs.com/png.latex?h=g(X\cdot \theta))
![](http://upload-images.jianshu.io/upload_images/3205350-45ea5c7a02fd42c8.latex?imageMogr2/auto-orient/strip)

利用梯度下降算法我们可以得到参数的变化方程：
![](http://latex.codecogs.com/png.latex?\theta_j := \theta_j -\alpha \frac{\partial}{\partial \theta_j}J(\theta))

为了方便下面对cost function求偏导，我们先计算sigmoid function的导数：

![](http://upload-images.jianshu.io/upload_images/3205350-e94985f594aaf903.latex?imageMogr2/auto-orient/strip)

然后求代价函数的偏导数：
![](http://upload-images.jianshu.io/upload_images/3205350-fb023f23277326d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
（对，很长...近乎绝望...但是并不难）
因此，对代价函数的偏导向量化之后得到：

![](http://latex.codecogs.com/png.latex?\bigtriangledown J(\theta)=\frac{1}{m}X^T [g(X \theta)-\vec{y}\ ])

<h4>最后</h4>

这一节的数学推导可能略难一点，请好好消化，如果有不明白的地方多去问问度娘==
下一节就用matlab实现上述算法。
