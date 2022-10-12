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

#### **摘要**
使用了一个粗粒度的模型对部分属性先进行预测和迭代得到一个部分的种群，再用当前细粒度种群填补空缺的属性，混合两个种群，再对该种群使用回归模型预测目标值，使用NSGA的环境选择方式更新种群，直到iter达到最大，最后再使用基于RVEA的方式得到最终的PF

和NSGA-Ⅱ_GP在大体框架下很相似

#### **创新点**
1、使用了知识迁移这一方法，也就是前一个模型的训练结果可以给到后一个模型作为输入
2、最后得到的解全部都在一个archive里，使用一个聚类求平均的方式筛选得到解

**<font size="3">基本框架</font>**
<center><img src="/images/2022-10/MS-RV.jpg" alt="img" style="zoom:70%" /></center>
<center><font size="1" face='KaiTi'>图1 MS-RV的框架图示 </font></center>  

1、初始化
根据已有的离线数据建立一个RBF的回归模型FS（fine surrogate），初始化种群

2、CS（coarse surrogate）的构建和使用
随机选择几个决策变量的特征作为CS的输入变量，建立一个二阶的多项式模型训练CS，并且迭代$g_{max}$代，每一代都使用CS来进行更新，使用联赛策略选择父代，使用遗传方式产生子代

3、FS的使用
将CS最后得到的种群用FS上一次迭代得到的种群填充未被CS搜索的特征，并将两个种群合并，使用RBF评估目标值，加入到DB中

4、停止
重复2、3直到iter达到最大。最后在DB中的所有种群以及它们所对应的目标值，使用基于RVEA的的方式权重向量的方式对目标值分类并求每个类的最大值作为该类的解，一个权重向量与一个类关联，最后得到的所有个体即构成PFS（pareto optimum solution set）。


</font>