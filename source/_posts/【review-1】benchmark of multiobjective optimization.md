---
title: 多目标测试问题小总结
categories:
  - 多目标优化
tags:
  - 数据驱动
  - 多目标优化
  - 知识总结
data: 2022-10-26 20:58:00
---

## 多目标测试问题小结
参考文献
[1]S. Huband, P. Hingston, L. Barone and L. While, "A review of multiobjective test problems and a scalable test problem toolkit," in IEEE Transactions on Evolutionary Computation, vol. 10, no. 5, pp. 477-506, Oct. 2006, doi: 10.1109/TEVC.2005.861417.

原文问题定义和公式比较详细，详情搜索原文去查阅~~
### 测试问题
“工欲善其事，必先利其器”，既然想要实现一个比较好的多目标优化算法，那就要找到能够比较公平判断该算法的问题，而如果需要比较多个算法，也应该公平地在同一个问题上去实验。
我们通常把这类问题称为基准问题（benchmark）或者测试问题(test problem)

一般来说，作为一个测试问题，包含以下几个方面的特性。
<center><img src="/images/2022-10/问题特征.jpg" alt="img" style="zoom: 80%"></center>

下面一条条讲一下。
R1--R7：是作为一个比较好的基准问题的建议具备的特征
F1--F5：是一些进阶特征
#### R1、No Extremal Parameters
一个基准问题中，不应该让这个问题的最优解在其决策变量的边界被取到。
理解一下，这样特别容易在进化算法某代某次突然的变异偶然得到问题的最优解，在使用例如截断选择策略时就往往要比使用最近邻替换的策略方式效果更好，但是如果换一个基准问题的话，使用截断选择策略的EA效果往往就会下降。也就是说这样的基准问题具有比较大的测试偶然性，不推荐。

#### R2、No Medial Parameters
一个基准问题，不应该有中间变量。
（没有很懂这个。。。）
就是说一个初始种群，其中的分布对于每个决策向量的每一个维度来说都是均匀随机的，如果包含这种madial变量的话，在重组之后会偏向更优解

#### R3、 Scalable Number of Parameters
一个基准问题里，决策向量的维数应该是可以改变的（scalable），方便测试不同维度下算法对该问题的性能

#### R4、Scalable Number of Objectives
一个基准问题里，目标的个数应该也是可扩展的，与R3一致

#### R5、Dissimilar Parameter Domains
在一个基准问题中，不同决策变量的可行域应该是不同的。
这可以要求对应的变异因子的缩放强度也相应变化（取决于使用的变异方式）

#### R6、Dissimilar Tradeoff Ranges
一个基准问题的每一个目标函数的最优范围应该不是同一个区间

#### R7、Pareto Optima Known
pareto前沿最好是已知的，这样便于算法的一些指标比较

#### F1、Pareto Optimal Geometry
pareto最优的几何指标，包括pareto前沿的形状，包括convex(凸的)、concave（凹的）、linear（线性的）、mixed（多种混合的）、degenerate（退化的）、disconnected（不连续的）、connected（连续的）等包括以上几种的集合。
<center class="half">
<figure>
<img style="float:left" src="/images/2022-10/convex.svg" alt="img" width="210" />
<img style="float:left" src="/images/2022-10/concave.svg" alt="img" width="210" />
<img src="/images/2022-10/disconnected.svg" alt="img" width="210" />
</figure>
</center>

退化的主要是指一个M目标的问题得到PF是M-1维的

#### F2、Parameter Dependencies
决策向量的每一维度尽量是独立分布的

#### F3、Bias
偏差决定了这个问题收敛到pareto最优的速度，要给基准问题上难度就加这一项

#### F4、Many-to-One Mappings
同样也是给基准问题上难度的特征，如果是一个多对一的问题，在进行最优解比较的时候将导致偏差。
还存在一种现象就是适应度地形(fitness lanscape)中的flat region问题，在一个相对较平坦的区域，物种的突变方向将难以确定，也就是说可能每个方向对于该问题来说都是一个潜在的优秀解，这样的问题在进化搜索的过程中收敛的速度就会比较慢或是最后得到的不是一个比较好的解集。
适应度地形大概长这样
<center><img src="/images/2022-10/fitness lanscape.jpg" alt="img" style="zoom: 80%"></center>

[适应度地形参考文章](https://zhuanlan.zhihu.com/p/20445783)


#### F5、Modality

一个问题是单峰（unimodal）还是多峰（multimodal），也就是问题的极值点。而多峰问题就意味着可能会有很多局部最优的解，但是多峰问题更代表现实中的实际问题，这类问题收敛也比较慢，甚至进入局部最优而停止进化（参考适应度地形文章）

### 几个常见的测试问题集
#### 1、ZDT集
PF参考：《Comparison of Multiobjective Evolutionary Algorithms: Empirical Results》
用得很广泛，大多数研究双目标优化问题会使用这类问题作为基准问题
下面是ZDT问题的定义，这里没有ZDT5，因为它是以二进制的编码，不在我们考虑范围内
<center><img src="/images/2022-10/ZDT-overview.jpg" alt="img" style="zoom: 80%"></center>

每个问题的特征
<center><img src="/images/2022-10/ZDT-features.jpg" alt="img" style="zoom: 80%"></center>

ZDT3：不连续的
ZDT4：有很多的局部PF
ZDT6：多峰问题
注意，这几个函数的$f_1$都只包含一个变量

**特征与局限：**
* 所有问题都只有固定的2个目标函数
* 没有退化的PF
* 没有不可分（Nonseparable）的问题
* 没有欺骗性的问题（deceptive）
* 没有问题的适应度地形是具有平坦区域的
* 只有作为距离参数的决策变量是可扩展的
* 所有问题除了ZDT4，都是在变量的边界上取到极值，甚至它们都是内侧变量

#### 2、DTLZ集
PF参考：《Scalable Multi-Objective Optimization Test Problems》
用得也很广泛，基本上研究多目标优化的文章都会用到这个基准问题集，因为它可以扩展的目标数和可以改变的决策变量个数，所以使用起来比较灵活
下面是DTLZ问题的定义，DTLZ8和9是有限制的问题，此处暂时不讨论有限制的优化问题
<center><img src="/images/2022-10/DTLZ-overview.jpg" alt="img" style="zoom: 80%"></center>

每个问题的特征
<center><img src="/images/2022-10/DTLZ-features.jpg" alt="img" style="zoom: 80%"></center>

DTLZ5和DTLZ6在目标数大于3时的性质无法确定，所以打了问号;在M=3时其PF是退化的


**特征与局限：**
* 可以扩展目标个数
* 可以扩展变量个数，并且前M-1个目标的变量是固定的，只有第M个目标的变量是可以改变的
* 没有不可分（Nonseparable）的问题
* 没有欺骗性的问题（deceptive）
* 没有问题的适应度地形是具有平坦区域的


#### 3、MOP集

下面是MOP问题的定义，这些问题不是来自同一个研究者，而是一些研究者根据以往的问题改进之后得到的问题
<center><img src="/images/2022-10/MOP-overview.jpg" alt="img" style="zoom: 80%"></center>

每个问题的特征
<center><img src="/images/2022-10/MOP-features.jpg" alt="img" style="zoom: 80%"></center>

MOP3是个不可分的问题，且是多峰的，所以它的PF比较难确定，pareto前沿是凸的
MOP4的最优解集是非平凡的，pareto前沿包含一个退化的点和一些混合凹凸的线
MOP5的pareto前沿则是一个退化的凸的线和一个退化的混合凹凸的线构成
MOP6的pareto前沿是由凸的和混合凹凸的线构成
MOP7的pareto前沿是连续的，表现为凸的，但是有某部分又退化成线

**特征和局限：**
* 只有两个或三个目标函数
* 没有不可分离的问题的决策变量是可变尺度的
* 没有问题的目标数量是可变的
* 由于它们的临时天性（ad hoc nature），所以问题的分析比较困难
* 没有欺骗性的问题（deceptive）
* 没有问题的适应度地形是具有平坦区域的
* 没有many-to-one的问题

#### 4、WFG集
比较新的一个测试问题集，用的不太多，一般都是算法如果在某几个问题上表现特别优秀，也许那篇文章会放上这个问题。
我还没怎么用过这个问题集，如果有更新再放上来

WFG问题定义
<center><img src="/images/2022-10/WFG-overview.png" alt="img" style="zoom: 80%"></center>

每个问题的特征
<center><img src="/images/2022-10/WFG-features.jpg" alt="img" style="zoom: 80%"></center>

**结语**
当前用在多目标上的基准问题是各式各样的，但是比较经典常见的就是那一些，一般来说，没有什么差错，基本上就是ZDT和DTLZ就可以了，如果算法表现刁钻，在某些问题上很好，也许可以拿来说说。因为不同问题的特性不一样，所以一般要进行对比最好整个集合的问题都对比，不要只比较其中的某几个，很容易有失偏颇。
文章里还列举了很多数目比较尴尬，够不上一个集合但是又不是单个的，也列举了很大一部分，可以参考一下，如果没有能耐自己造出问题，那么用这些问题就足够了。
关于具体的算法在特定问题上的表现有好有坏，这在实验过程中也是值得探讨的问题，后面单独再讲。