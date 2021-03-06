---
layout: post
title: "计算几何题集（转载）"
author: Ricardo
tags: 计算几何
---

**计算几何题的特点与做题要领：1.大部分不会很难，少部分题目思路很巧妙2.做计算几何题目，模板很重要，模板必须高度可靠。3.要注意代码的组织，因为计算几何的题目很容易上两百行代码，里面大部分是模板。如果代码一片混乱，那么会严重影响做题正确率。4.注意精度控制。5.能用整数的地方尽量用整数，要想到扩大数据的方法（扩大一倍，或扩大sqrt2）。因为整数不用考虑浮点误差，而且运算比浮点快。**

 

**一、点，线，面，形基本关系，点积叉积的理解****POJ 2318 TOYS（推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2318**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2318)
**POJ 2398 Toy Storage（推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2398**
一个矩形，有被若干直线分成N个格子，给出一个点的坐标，问你该点位于哪个点中。
知识点：其实就是点在凸四边形内的判断，若利用叉积的性质，可以二分求解。
POJ 3304 Segments
[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3304**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3304)
**知识点：线段与直线相交，注意枚举时重合点的处理POJ 1269 Intersecting Lines **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1269**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1269)
**知识点：直线相交判断，求相交交点****POJ 1556 The Doors （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1556**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1556)
**知识点：简单图论＋简单计算几何，先求线段相交，然后再用Dij求最短路。POJ 2653 Pick-up sticks **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2653**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2653)
**知识点：还是线段相交判断POJ 1066 Treasure Hunt **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1066**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1066)
**知识点：线段相交判断，不过必须先理解“走最少的门”是怎么一回事。POJ 1410 Intersection **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1410**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1410)
**知识点：线段与矩形相交。正确理解题意中相交的定义。详见：**[**http://hi.baidu.com/novosbirsk/blog/item/68c682c67e8d1f1d9d163df0.html**](http://hi.baidu.com/novosbirsk/blog/item/68c682c67e8d1f1d9d163df0.html)
**POJ 1696 Space Ant （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1696**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1696)
**德黑兰赛区的好题目。需要理解点积叉积的性质POJ 3347 Kadj Squares **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3347**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3347)
**本人的方法极度猥琐。复杂的线段相交问题。这个题目是计算几何的扩大数据运算的典型应用，扩大根号2倍之后就避免了小数。**
**POJ 2826 An Easy Problem?! （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2826**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2826)
**问：两条直线组成一个图形，能容纳多少雨水。很不简单的Easy Problem，要考虑所有情况。你不看discuss看看能否AC。（本人基本不能）提示一下，水是从天空垂直落下的。POJ 1039 Pipe **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1039**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1039)
**又是线段与直线相交的判断，再加上枚举的思想即可。POJ 3449 Geometric Shapes **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3449**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3449)
**判断几何体是否相交，不过输入输出很恶心。此外，还有一个知识点，就是给出一个正方形（边不与轴平行）的两个对角线上的顶点，需要你求出另外两个点。必须掌握其方法。POJ 1584 A Round Peg in a Ground Hole **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1584**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1584)
**知识点：点到直线距离，圆与多边形相交，多边形是否为凸****POJ 2074 Line of Sight （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2074**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2074)
**与视线问题的解法，关键是求过两点的直线方程，以及直线与线段的交点。数据有一个trick，要小心。**

 

 

**二、凸包问题POJ 1113 Wall **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1113**
**知识点：赤裸裸的凸包问题，凸包周长加上圆周。POJ 2007 Scrambled Polygon **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2007**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2007)
**知识点：凸包，按极角序输出方案****POJ 1873 The Fortified Forest （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1873**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1873)
**World Final的水题，先求凸包，然后再搜索。由于规模不大，可以使用位运算枚举。详见：**[**http://hi.baidu.com/novosbirsk/blog/item/333abd54c7f22c52574e0067.html**](http://hi.baidu.com/novosbirsk/blog/item/333abd54c7f22c52574e0067.html)
**POJ 1228 Grandpa's Estate （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1228**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1228)
**求凸包顶点数目，很多人求凸包的模板是会多出点的，虽然求面积时能得到正确答案，但是在这个题目就会出问题。此外，还要正确理解凸包的性质。POJ 3348 Cows **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3348**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3348)
**凸包面积计算**

 

 

**三、面积问题，公式问题POJ 1654 Area **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1654**
**知识点：利用有向面积（叉积）计算多边形面积POJ 1265 Area **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1265**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1265)
**POJ 2954 Triangle **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2954**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2954)
**Pick公式的应用，多边形与整点的关系。（存在一个GCD的关系）**

 

 

**四、半平面交半平面交的主要应用是判断多边形是否存在核，还可以解决一些与线性方程组可行区域相关的问题（就是高中时的那些）。POJ 3335 Rotating Scoreboard**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3335**
**POJ 3130 How I Mathematician Wonder What You Are! **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3130**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3130)
**POJ 1474 Video Surveillance**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1474**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1474)
**知识点：半平面交求多边形的核，存在性判断POJ 1279 Art Gallery **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1279**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1279)
**半平面交求多边形的核，求核的面积****POJ 3525 Most Distant Point from the Sea （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3525**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3525)
**给出一个多边形，求里面的一个点，其距离离多边形的边界最远，也就是多边形中最大半径圆。可以使用半平面交+二分法解。二分这个距离，边向内逼近，直到达到精度。****POJ 3384 Feng Shui （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3384**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3384)
**半平面交实际应用，用两个圆覆盖一个多边形，问最多能覆盖多边形的面积。解法：用半平面交将多边形的每条边一起向“内”推进R，得到新的多边形，然后求多边形的最远两点。****POJ 1755 Triathlon （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1755**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1755)
**半平面交判断不等式是否有解。注意不等式在转化时正负号的选择，这直接影响到半平面交的方向。POJ 2540 Hotter Colder **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2540**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2540)
**半平面交求线性规划可行区域的面积。POJ 2451 Uyuw's Concert**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2451**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2451)
**Zzy专为他那篇nlogn算法解决半平面交问题的论文而出的题目。**

 

 

**五、计算几何背景，实际上解题的关键是其他问题（数据结构、组合数学，或者是枚举思想）若干道经典的离散化＋扫描线的题目，ACM选手必做题目****POJ 1151 Atlantis （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1151**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1151)
**POJ 1389 Area of Simple Polygons**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1389**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1389)
**矩形离散化，线段树处理，矩形面积求交POJ 1177 Picture （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1177**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1177)
**矩形离散化，线段树处理，矩形交的周长，这个题目的数据比较强。线段树必须高效。 ****POJ 3565 Ants （推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3565**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3565)
**计算几何中的调整思想，有点像排序。要用到线段相交的判断。详见：**[**http://hi.baidu.com/novosbirsk/blog/item/fb668cf0f362bec47931aae2.html**](http://hi.baidu.com/novosbirsk/blog/item/fb668cf0f362bec47931aae2.html)
**POJ 3695 Rectangles    **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3695**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3695)
**又是矩形交的面积，但是由于是多次查询，而且矩形不多，使用组合数学中的容斥原理解决之最适合。线段树是通法，但是除了线段树，还有其他可行的方法。POJ 2002 Squares    **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2002**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2002)
**枚举思想，求平面上若干个点最多能组成多少个正方形，点的Hash****POJ 1434 Fill the Cisterns!（推荐）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1434**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1434)
**一开始发昏了，准备弄个线段树。其实只是个简单的二分。**

 

 

**六、随机算法POJ 2420 A Star not a Tree? **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2420**
**多边形的费马点。所谓费马点，就是多边形中一个点P，该点到其他点的距离之和最短。四边形以上的多边形没有公式求费马点，因此可以使用随机化变步长贪心法。详见：**[**http://hi.baidu.com/novosbirsk/blog/item/75983f138499f825dd54019b.html**](http://hi.baidu.com/novosbirsk/blog/item/75983f138499f825dd54019b.html)
****

 

 

 

**七、解析几何这种题目本人不擅长，所以做得不多，模板很重要。当然，熟练运用叉积、点积的性质还是很有用的。POJ 1375 Intervals **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1375**
**知识点：过圆外一点求与圆的切线POJ 1329 Circle Through Three Points    **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1329**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1329)
**求三角形外接圆POJ 2354 Titanic**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2354**](http://acm.pku.edu.cn/JudgeOnline/problem?id=2354)
**求球面上两个点的距离，而且给的是地理经纬坐标。POJ 1106 Transmitters**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1106**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1106)
**角度排序，知道斜率求角度，使用atan函数。POJ 1673 EXOCENTER OF A TRIANGLE**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1673**](http://acm.pku.edu.cn/JudgeOnline/problem?id=1673)
**可以转化为三角形的垂心问题。**

 

 

 

**八、旋转卡壳POJ 2187 Beauty Contest **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=2187**
**凸包求最远点对。可以暴力枚举，也可以使用旋转卡壳。POJ 3608 Bridge Across Islands（难）**[**http://acm.pku.edu.cn/JudgeOnline/problem?id=3608**](http://acm.pku.edu.cn/JudgeOnline/problem?id=3608)
**两个凸包的最近距离。本人的卡壳始终WA。郁闷。**

 

 

 

**九、其他问题POJ 1981 Circle and Points **[**http://acm.pku.edu.cn/JudgeOnline/problem?id=1981**

求单位圆最多能覆盖平面上多少个点