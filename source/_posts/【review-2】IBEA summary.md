---
title: 基于指标的多目标进化算法（IBEA）简介
categories:
  - 知识总结
tags:
  - 多目标优化
  - 经典算法

data: 2022-10-28 15:14:00
---

### 基于指标的多目标进化算法（IBEA）简介

参考文献：
[1]Zitzler, Eckart and Simon Künzli. “Indicator-Based Selection in Multiobjective Search.” PPSN (2004).
[2]Jesús Guillermo Falcón-Cardona and Carlos A. Coello Coello. 2020. Indicator-based Multi-objective Evolutionary Algorithms: A Comprehensive Survey. ACM Comput. Surv. 53, 2, Article 29 (March 2021), 35 pages. https://doi.org/10.1145/3376916
[3]E. Zitzler, L. Thiele, M. Laumanns, C. M. Fonseca and V. G. da Fonseca, "Performance assessment of multiobjective optimizers: an analysis and review," in IEEE Transactions on Evolutionary Computation, vol. 7, no. 2, pp. 117-132, April 2003, doi: 10.1109/TEVC.2003.810758.


### Abstract
1、比起基于支配或是分解的多目标进化算法，基于指标的好处在于根据所选取指标的不同，可以在优化过程中就将用户偏好信息（preference information）考虑进去。
2、不需要任何额外的多样性保持的机制

### 指标选择
也就是说其实什么指标都可以用进来，选一个合适的指标就能套用一个通用的方法，这里就以一种指标为例讲述一个通用的方法。下面以参考文献[2]为例进行描述。

### $\epsilon$指标
**1、支配关系**
我们是怎么定义最初的支配关系的呢？
有这样的一个问题，给定一个的决策向量$\bm {x}=\{x_1,x_2,...,x_n\}$，需要优化的问题为$min \{f_i(\bm {x})\quad  fori=1,2,..,m\}$
现在有两个决策向量$\bm {x_1}$和$\bm{x_2}$，如果满足
$$\forall i \in m, f_i(\bm {x_1}) \leq f_i(\bm {x_2})$$
$$\exists j \in m,f_i(\bm {x_1}) < f_i(\bm {x_2})$$
那么就说$\bm {x_1}$支配$\bm{x_2}$，记作$\bm {x_1} \prec\bm{x_2}$（不失一般性，这里与min的目标一致，采用这种写法），在有些论文里也记作$\bm {x_1} \succ \bm{x_2}$。此时$\bm {x_1}$称为非支配的，$\bm {x_2}$称为被支配的。

这样定义的支配关系在目标个数较少的情况下还是比较有用的，但是目标数一旦多起来，如此严格的支配关系将几乎无法满足，也就是两两个体之间几乎都是互不支配的关系，那这个定义显然就鸡肋了嘛。

所以我们尝试放宽这个支配关系的标准，就有弱支配（weakly dominant）定义出现。而$\epsilon$指标的出现也是基于这样的想法。

**2、$\epsilon$指标**
这个指标提出其实是比较两个pareto set的好坏的，每一个pareto set里包含了很多个决策向量
$$I_{\epsilon}(A,B)=min_{\epsilon} \{\forall \bm{x_2}\in B, \exists \bm{x_1} \in A: f_i(\bm {x_1}) - \epsilon \leq f_i(\bm {x_2}) \quad for\; i \in \{ 1,2,...,m\}\}$$
如果每个pareto set里只有一个决策向量，那么上式就变为如下定义
$$I_{\epsilon}(\bm{x_1},\bm{x_2})=max\{ f_i(\bm {x_1}) - f_i(\bm {x_2}) \quad for\; i \in \{ 1,2,...,m\}\}$$

在这个指标的定义下，我们定义两个个体的支配关系就有如下两种方式：
对于两个决策向量$\bm {x_1}$和$\bm{x_2}$，如果满足（i）或者（ii）任意一个，那么我就说$\bm {x_1} \prec\bm{x_2}$
$$I_{\epsilon}(\bm{x_1},\bm{x_2}) < I_{\epsilon}(\bm{x_2},\bm{x_1}) \tag{i}$$
$$\forall \bm {x_3},\quad I_{\epsilon}(\bm{x_3},\bm{x_1}) \geq I_{\epsilon}(\bm{x_3},\bm{x_2}) \tag{ii}$$

公式说完了，现在来理解一下这个指标。
其实这就相当于是一个弱支配关系，按照严格的支配关系无法定义支配，那就尝试减掉一个松弛因子，这里我们记作$\epsilon$，然后两个个体间，谁减掉的松弛因子比较小的，那谁就更趋向一个非支配的状态。所以我们可以理解$I_{\epsilon}(\bm {x_i},\bm {x_j})$代表$\bm {x_i}$要支配$\bm {x_j}$需要减掉的松弛因子。

**3、其它指标**
还有很多种指标，比如超体积（hypervolume）、反转世代距离（Inverted Generational Distance）等一般的二元指标基本都可作为IBEA算法的指标来使用

### 基于指标的选择
#### 1、分配适应度
每个个体的适应度是由下式定义的，对于种群P，每个个体是一个决策向量
$$F(\bm {x_i})=\sum _{\bm {x_j} \in P \backslash \bm {x_i}} {-e^{-I(\bm {x_j},\bm {x_i})/(c(\bm {x_j}) \times k)}}$$
这里的k是一个定值，一般取k=0.05
$$c(\bm {x_j})=\underset {x_i \in P} {max}|I(\bm {x_i},\bm{x_j})|$$

来理解一下这个适应度公式，对于一个个体$\bm {x_i}$来说，它的适应度就是这个种群中所有其他个体需要支配它需要的松弛因子进行一个比例缩放之后的和，这里取了正号和负指数运算，是考虑到min的目标和比较以及符号的方向一致性。
那么现在还可以使用
$$F(\bm {x_1})>F(\bm {x_2}) \tag{iii}$$
来表示$\bm {x_1} \prec\bm{x_2}$

#### 2、环境选择
* 父代种群P经过一些遗传方式产生子代之后得到一个中间种群$P^*$，对于$P^*$中的每一个个体分配一个适应度值，排序之后找到适应度值最小的个体，将其踢出种群
* 重新更新当前种群每个个体的适应度
* 重复上述步骤直到$|P^*|=|P|$
* 有些算法这里会有一步mating selection，也就是说种群P是不是所有个体都用来生成后代，而是通过一些选择策略选择N个个体生成子代
* 得到当前代种群，继续进行进化更新直到gens达到停止条件或其他停止条件