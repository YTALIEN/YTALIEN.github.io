---
title: 基于指标的kriging和RBFN自适应选择离线数据驱动多目标优化算法
categories:
  - 多目标优化相关论文精读
tags:
  - 数据驱动
  - 多目标优化
  - 回归模型
  - off-line data-driven

data: 2022-10-28 15:10:00
---


## 《Performance Indicator-Based Adaptive Model Selection for Offline Data-Driven Multiobjective Evolutionary Optimization》
Z. Liu, H. Wang and Y. Jin, "Performance Indicator-Based Adaptive Model Selection for Offline Data-Driven Multiobjective Evolutionary Optimization," in IEEE Transactions on Cybernetics, doi: 10.1109/TCYB.2022.3170344.

### abstract
本文主要是想要着眼于离线的数据驱动多目标优化中模型的误差会导致的不准确的进化方向这个问题，提出了基于指标的进化算法和使用自适应选择kriging或是rbfn回归作为代理模型进行预测。

### introduction
对比起单目标问题来说，
1、大数据量的多目标问题更需要一个multifidelity surrogates来平衡计算真实适应度函数的复杂度和模型的质量
2、对于少量的数据集，增强模型的准确性和鲁棒性
Kriging模型：可以提供数据的不确定信息（预测的置信区间）

#### 我的思考
**1、具体是怎么实现这个所谓的自适应呢？**
这篇文章，考虑了模型的不准确性但是不多，其实并没有彻底解决这个问题，但是表现在最终效果上比单纯使用一个回归或是简单的集成效果稍微要好。
以往来说，一个比较好的思路也是比较容易想到的思路，一个模型的准确度低，那就弄多个呗，也就是ensemble，这篇文章的核心思想也是这个。
但是他比较创新的地方在于这个ensemble，不是所有模型都用上做辅助选择，而是默认先用kriging，当kriging准确度较低之后，就选择RBFN，每一次迭代都这样比较一下，krging不行就用RBFN。
不同问题下两个模型各有千秋，自适应啊，就是针对不同的问题自主选择合适的模型，在每一个迭代里选择合适的模型。




#### 基本框架
<center><img src="/images/2022-10/IBEA-MS-framework.jpg" alt="img" style="zoom: 100%"></center>

#### 实现细节
**1、自适应模型选择**
起名的艺术（￣︶￣）↗　，就大概来看看他讲了个啥故事
这个kriging模型，返回的其实是一个以预测的值为中心，以一个模型得到值为方差的正态分布，根据$3\sigma$原则，可以判断两个个体，在这个目标上的相对关系是否是可信的，最后对所有目标加和判断可信的目标个数，以判断该模型到底有没有效果，没有就换RBFN，至于RBFN有没有效果或是准确率也低的办法，这里也没有说...这是个局限。

**2、基于指标的选择**
详见我的IBEA那篇文章（可站内搜索）