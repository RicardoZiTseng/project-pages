---
layout: post
title: 机器学习与深度学习入门（三）
author: Ricardo
tags:	matlab machine_learning
---

> 第三篇就应用代码进行实战了，加油 \o^o/

首先装载数据，并将数据分别存入X与y中,并将数据可视化：
{% highlight javascript %}
data=load('ex1data1.txt');
X = data(:,1); y=data(:,2);
figure;
plot(X,y,'rx','MarkerSize',10);
{% endhighlight %}

![](http://upload-images.jianshu.io/upload_images/3205350-52d67ef11727443f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后对X进行预处理，使其第一列的值都为1

{% highlight javascript %}
m=size(X,1);
X=[ones(m,1) X];
{% endhighlight %}

初始化参数值：

{% highlight javascript %}
theta=zeros(2,1);
{% endhighlight %}

假设我们需要对数据集训练1500次，学习速率设为0.01:

{% highlight javascript %}
iterations=1500;
alpha=0.01;
{% endhighlight %}

编写梯度下降算法：

{% highlight javascript %}
function [theta,J_history] = gradientDescent(X,y,theta,alpha,iterations)
m=size(X,1);
J_history = zeros(iterations,1);
  for i =1:iterations:
    temp1 = theta(1)-(alpha/m)*sum((X*theta-y).*X(:,1));
    temp2 = theta(2)-(alpha/m)*sum((X*theta-y).*X(:,2));
    theta(1) = temp1;
    theta(2) = temp2;
    J_history(i) = computeCost(X,y,theta);
  end
end
{% endhighlight %}

当然我们还需要编写cost function的代码：
{% highlight javascript %}
function J = computeCost(X,y,theta)
  m = size(X,1);
  J = (X*theta-y)'*(X*theta-y)/(2*m);
end
{% endhighlight %}

ok,我们完成上述代码之后，开始对训练集应用梯度下降算法进行训练：
{% highlight javascript %}
theta = gradientDescent(X,y,theta,alpha,iterations);
{% endhighlight %}

我们将计算出来的曲线拟合在图表上：
{% highlight javascript %}
hold on;
plot(X(:,2),X*theta,'-');
{% endhighlight %}

![](http://upload-images.jianshu.io/upload_images/3205350-2aa54047e35e8a4c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**正规化方程**
之前提到，如果样本数据值过大的话我们需要对他们进行一些预处理，使他们的值变小，以方便应用梯度下降算法，这里我直接采用正规化方程，来计算他们的拟合曲线。

载入数据
{% highlight javascript %}
data=load('ex1data2.txt');
X = data(:,1:2);
y = data(:,3);
m = size(X,1);
X = [ones(m,1) X];
{% endhighlight %}

编写正规化方程的代码：
{% highlight javascript %}
theta = pinv(X'*X)*X'*y;
{% endhighlight %}

计算一下此时的代价函数值：

{% highlight javascript %}
 J = (X*theta-y)'*(X*theta-y)/(2*m);
{% endhighlight %}

此时其代价值应该在10的负3次方数量级左右。

**最后**
下一篇教程将开始考虑逻辑回归的问题，公式可能更加复杂，加油~

