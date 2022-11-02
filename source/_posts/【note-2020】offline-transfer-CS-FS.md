---
title: 使用粗、细粒度的多回归模型的离线多目标数据驱动
categories:
  - 多目标优化相关论文精读
tags:
  - 数据驱动
  - 多目标优化
  - 回归模型
  - off-line data-driven
abbrlink: b118000d
data: 2022-10-11 16:22:00
---

### 《Off-line Data-driven Multi-objective Optimization: Knowledge Transfer between Surrogates and Generation of Final Solutions》
出处：C. Yang, J. Ding, Y. Jin and T. Chai, "Offline Data-Driven Multiobjective Optimization: Knowledge Transfer Between Surrogates and Generation of Final Solutions," in IEEE Transactions on Evolutionary Computation, vol. 24, no. 3, pp. 409-423, June 2020, doi: 10.1109/TEVC.2019.2925959.

#### abstract
使用了一个多项式回归模型对部分属性先进行预测和迭代进化得到一个部分的种群，再用当前细粒度种群填补空缺的属性，混合两个种群，再对该种群使用全局的RBF回归模型预测目标值，使用NSGA的环境选择方式更新种群，每次iter存一批解，最后再使用基于RVEA的方式聚类得到最终的PF

#### introduction
1、提取部分属性构建一个多项式回归
2、最后得到的解全部都在一个archive里，使用一个聚类求平均的方式筛选得到解


#### 我的思考
**1、它的主要创新点是？**
在得不到新的数据，又面临着全局模型后期local的错误率的情况下，它当然也有一个全局的模型，但是它主要是那个local的模型，一个多项式回归模型，但是随机选一些特征进行构建优化，套在NSGA-Ⅱ皮上，大概演化到一定程度之后再给全局的模型去细调一下，这个过程不是一次就结束，而是这样重复n次，最后全部解里选部分输出。
之后就是它的细节处理部分了。

**<font size="3">基本框架</font>**
<center><img src="/images/2022-10/MS-RV.jpg" alt="img" style="zoom:70%" /></center>
<center><font size="1" face='KaiTi'>图1 MS-RV的框架图示 </font></center>  

1、初始化，建立global模型
根据已有的离线数据建立一个RBF的回归模型FS（fine surrogate），初始化种群

2、CS（coarse surrogate）的构建和使用
随机选择几个决策变量的特征作为CS的输入变量，建立一个二阶的多项式模型训练CS，并且迭代$g_{max}$代，每一代都使用CS来进行更新，使用联赛策略选择父代，使用sbx和pm生成子代，再基于NSGA-Ⅱ进行环境选择

3、FS的使用
将CS最后得到的种群用FS上一次迭代得到的种群填充未被CS搜索的特征，并将两个种群合并，使用RBF评估目标值，加入到DB中

4、停止
重复2、3直到iter达到最大。最后在DB中的所有种群以及它们所对应的目标值，使用基于RVEA的的方式权重向量的方式对目标值分类并求每个类的最大值作为该类的解，一个权重向量与一个类关联，最后得到的所有个体即构成PFS（pareto optimum solution set）。


</font>