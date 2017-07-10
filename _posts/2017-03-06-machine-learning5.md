---
layout: post
title: 机器学习与深度学习入门（五）
author: Ricardo
tags:	matlab machine_learning neural_network
---

> 这节开始了解神经网络算法，但是神经网络算法的来源以及形象化的解释可以参考我之前写的[利用神经网络识别手写数字](https://ricardozitseng.github.io/network/)这篇文章，本文通过数学工具将神经网络算法抽象化，以获得更加简洁且有效的算法。

在开始讨论之前，我们再次看一次神经网络的图：

![](http://upload-images.jianshu.io/upload_images/3205350-023cc0b98cf5cd5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

​	还记得我们的sigmoid function吗，在神经网络中神经元内的激励函数也是sigmoid函数：$\frac{1}{1+e^{-\theta^{T}X}}$, 对于$\theta$参数来说，在神经网络中也被称为权重，也就是我在《利用神经网络识别手写数字》中经常谈到的bias。

​	接下来，我们需要假设一些数学标记以方便接下来的数学推导：

​				$a^{(j)}_{i}:$ 第j层的第i个单元的激励项

​				$\Theta^{(j)}$: 控制第j层单元到第j+1层单元之间的权重值

​	对于一个三层神经网络来说，假设第一层有三个输入项，第二层有三个激励项单元，第三层有一个输出项，那么它的整个计算过程就像下面这样：

​							$\begin{bmatrix}x_0 \newline x_1 \newline x_2 \newline x_3\end{bmatrix}\rightarrow\begin{bmatrix}a_1^{(2)} \newline a_2^{(2)} \newline a_3^{(2)} \newline \end{bmatrix}\rightarrow h_\theta(x)$

具体来说，每个激励项的数学表达式为：

​				$a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3)$	

​				$a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3) $

​				$a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3)$

​			$h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)})$

进一步的简化上面的式子，令：

​				$z^{(2)}_{1} = \Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3$

​				$z^{(2)}_{2} = \Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3$

​				$z^{(2)}_{3} = \Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3$

​				$z^{(3)}_{1} = \Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)}$

所以有：

​				$a^{(2)}_{1} = g(z^{(2)}_{1})$	$a^{(2)}_{2} = g(z^{(2)}_{2})$	$a^{(2)}_{3} = g(z^{(2)}_{3})$

对于其他层的其他单元来说，z可以被表示为：

​				$z^{(j)}_{k} = \Theta^{(j-1)}_{k,0}x_0 + \Theta^{(j-1)}_{k,1}x_1 +...+ \Theta^{(j-1)}_{k,n}x_n$

