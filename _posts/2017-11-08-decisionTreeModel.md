---

layout: post
title: "机器学习之决策树"
subtitle: "\"Introduction!\""
date: 2017-11-08
author: "fibears"
header-img: "img/in-post/header/post-bg-treemodel.jpg"
tags:
- Machine Learning
- Tree Model

---

## 简介

决策树是一种常见的机器学习方法，常见的有ID3(Iterative dichotomiser), C4.5 和 CART(Classification And Regression Tree)。这几种算法都采用贪婪算法，自顶向下递归分冶构建分叉树模型。决策树模型的基本流程如下所示[1]:

```
Input: 训练集 D={(x1,y1),...,(xn,yn)}, 变量集 A={(a1,a2,...,ad)}
Process: 函数TreeGenerate(D,A)
1. 生成节点 node
2. if D 中样本属于同一类别 C Then
3.   将 node 标记为 C 类叶节点；return
4. end if
5. if A=null or D中样本在 A 上取值相同 then
6.   将 node 标记为叶子节点，其类别标记为 D 中样本数最多的类；return
7. end if
8. 从A中选择最优划分变量a*;
9. for a* 的每一个取值a do
10.   为 node 生成一个分支；令Dv表示D中在a*上取值为a的样本子集；
11.   if Dv 为空 then
12.     将分支节点标记为叶节点，其类别标记为D中样本最多的类；return
13.   else
14.     以 TreeGenerate(Dv, A\{a*}}) 为分支节点
15.   end if
16. end for
Output: 以 node 为根节点的一颗决策树
```

给定一个训练集D,决策树算法的计算复杂度为 \\(O(d*\|D\|*log(\|D\|))\\)，其中d是变量个数，\|D\|是训练集的样本数。

## 变量度量选择

从上述的算法流程中可以看出，该模型最重要的步骤是如何筛选出最佳的划分变量。决策树模型希望其分支节点所包含的样本尽可能属于同一类别，即节点的纯度越高越好。

### 信息增益——ID3

信息熵(Information Entropy)是度量样本集合纯度最常用的一种指标，假定当前样本集合D中第i类样本所占的比例为\\(p_i(i=1,2,...,n)\\)，则

$$
Info(D)=-\sum_{i=1}^{n}p_i*log_2(p_i)
$$

其中\\(p_i\\)是任意样本属于类别\\(C_i\\)的非零概率，Info(D)的值越小，表示D的纯度越高。

假定离散变量a有V个可能的取值\\(\{a^1,a^2,...,a^V\}\\)，若使用a来对样本集D进行划分，则会产生V个分支节点，第v个分支节点上包含了取值为\\(a^v\\)的样本，记为\\(D^v\\)，由于不同分支节点所包含的样本数不同，给分支节点赋予权重\\(|D^v|/|D|\\),因此利用变量a对样本集进行划分所得到的信息增益为

$$
Gain(D,A)=Ent(D)-\sum_{v=1}^{V}\frac{|D^v|}{|D|}Ent(D^v)
$$

一般来说，信息增益越大，说明使用变量a来进行划分所获得的纯度提升越大，经典的ID3决策树算法就是采用信息增益的准则来选择最优划分变量。

### 增益率——C4.5

由于信息增益准则倾向于选择取值数目较多的变量，根据样本编号可以将样本集分成n个组，此时每个组内只有一条样本，Info值为0，Gain值最大，但是这种分法没有意义。于是Quinlan提出的C4.5算法采用增益率来选择最优划分变量，增益率的公式为

$$
Gain\_ratio(D,A)=\frac{Gain(D,A)}{SplitInfo_{A}(D)}
$$

其中SplitInfo的定义类似于信息熵，其用于刻画利用变量A来划分数据集D所产生的信息

$$
SplitInfo_{A}(D)=-\sum_{v=1}^{V}\frac{|D^v|}{|D|}\log_2\frac{|D^v|}{|D|}
$$

因此，我们可以看出：增益率准则倾向于选择取值数目较少的变量。

### 基尼指数——CART
Breiman等人提出的CART决策树采用基尼指数来选择最优划分变量，他们采用基尼值来度量数据集的纯度，基尼指数越小，数据集的纯度越高。

$$
Gini(D)=\sum_{i=1}^{n}\sum_{i^`\not= i}p_ip_{i^`}=1-\sum_{i=1}^{n}p_{i}^{2}
$$

即从数据集D中随机抽取两个样本，其类别标记不一致的概率，因此变量A的基尼指数为

$$
Gini\_index(D,A)=\sum_{v=1}^{V}\frac{|D^v|}{|D|}Gini(D^v)
$$

CART选择使得划分后基尼指数最小的变量作为最优划分变量。

### Other 

除了上述三种方法外，还有基于其他指标来筛选最优划分变量的方法，比如算法CHAID使用卡方检验统计量来选择最优变量，其他方法还包含C-SEP和G-统计量等。
所有的统计量都具有某种程度上的偏倚，需要根据具体情况选择相应的度量方法，一般最常用的算法就是基于基尼指数的CART算法。

### 连续值与缺失值

由于连续变量的可取值数目不再有限，因此不能直接根据变量的可取值点来对节点进行划分，C4.5算法采用二分法对连续变量进行处理。

给定样本集D和连续变量a，假定a在D有n个不同的取值，将这些值从小到大排序后，记为\\(\{a^1,a^2,...,a^n\}\\)。对连续变量a，我们把区间\\([a^i,a^{i+1})\\)的中位点作为候选划分点，然后按照离散变量选择划分点的方法选择最优划分点，即

$$
Gain(D,a)=max_{t \in T_a}Gain(D,a,t)
$$

对于样本集中的缺失值，如果简单地将其过滤掉，将会造成信息的损失。C4.5算法的解决思路是让同一个样本以不同的概率划入到不同的子节点中。


## 剪枝过程
在构建决策树的时候，为了尽可能正确分类训练样本，许多分支反映的是训练集中自身的一些特性数据，这会导致过拟合，因此我们可以通过剪枝的方法来降低过拟合的风险。

决策树的剪枝策略有预剪枝和后剪枝两种方法，预剪枝是指在决策树生成过程中，对每个节点在划分前先进行估计，若当前节点的划分不能带来模型泛化性能提升，则停止划分过程，将当前节点标记为叶节点；后剪枝则是先从训练集生成一棵完整的决策树，然后自底向上地对非叶节点进行评估，若将该节点对应的子树替换为叶子节点能带来算法泛化性能的提升，则将该子树替换为叶节点。

## sklearn实现

```python
# import packages
from sklearn import tree
from sklearn.datasets import load_iris

# load iris dataset
iris = load_iris()

# model
clf = tree.DecisionTreeClassifier()
clf = clf.fit(iris.data, iris.target)

# predict
## class
clf.predict(iris.data[:1, :])
## probability
clf.predict_proba(iris.data[:1, :])

```




## Reference

[1] 周志华. 《机器学习》