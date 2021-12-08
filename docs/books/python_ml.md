---
layout: default
title: Python machine learning:machine learning and deep learning with Python, scikit-learn, and TensorFlow 2
parent: books
date:   2021-11-10 20:28:05 -0400
categories: ml
nav_order : 1
---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Chapter 2 : Training Simple Machine Learning Algorithms for Classification

在这一章中介绍了机器学习中的神经元的发展历史。在下图中我们可以看到两种神经元。

第一种是模拟人类神经元细胞的激活机制最早提出来的。输入端模拟了树突，然后整合所有的信号，与streshold 进行比较，来判定class label(1 or -1)。

第二种改进的地方是在net input之后增加了一个activation function。这样两种神经元在对weights 进行update时，updates 的来源是不同的。第一种直接来源于output，第二种来源于activation function。前者是一个整数，后者是一个float number。这样后者可以判定偏移正常class label 的程度。而第一种不可以。

![perceptron vs adaptive linear neuron](/assets/images/python_ml/perceptron.png)

同时这一章还介绍了为什么会有bias，在perceptron中为了使threshold=0.我们认为添加了一个bias，并且bias存放在weights数列的第一个。

weights在初始化时，其应该是很小的数，但不能为0，如果为0则书本上说其weights的变化只是scaler的变化，而没有方向上的变化。同时使用`w = np.random.norm(loc=0.0, scale=0.1, size = 1+ x.shape[1])`来初始化weights。

在写code时应培养良好的document。在本章中可以借鉴一下：

```md

“”“function or class definition,具体是干什么的

Parameters
------
p1 : type
    definition
p2 : type
    definition
    
Returns
-------
r1 : type
    definition
r2 : type

Examples
-------
give some simple examples

Other
-------
Description

“”“

```


## weight update

`w += det_w`


![weights update](/assets/images/python_ml/perceptron_update.png)

以上是第一种神经元Perceptron的weights进行update时，weights变化

![weights update](/assets/images/python_ml/ada_update.png)

以上是第二种神经元ada的weights 进行update时weights的变化量。其来源于以下cost 函数。只需要对wj进行求偏导就可以得到以上公式。

![weights update](/assets/images/python_ml/ada_cost.png)

两者的区别如下：
1. perceptron ： 每一个example(row)对weight进行更新一次； ada：公式里面有求和符号，因此是对所有的example遍历完之后再更新weights。后者叫做`Batch Gradient descent`。
2. 对于perceptron，`error = yi - y^i` 其中output是class label，即 1 or -1。而ada error 中的output 是一个float number。
3. perceptron：deta_w 中的x是一个row中的fetures。ada：是一个点积。

如果我们的数据量很大，那么遍历完所有的examples再更新weights则不实际。因此我们也可以每一个example 更新一次weights，这叫做`stochastic gradient descent`。

对于learning rate(eta)超参数。如果太大，则会发生overshoot，即在minumn点来回的走动。如果太小，则聚合太慢。

对于data中数据数量级不一致的情况，例如有的列为0.09，有的列为19800，那么显然直接使用两者的数据进行parameter fiting。后者影响会很大，而前者影响小。也会影响聚合速度。因此我们需要对数据进行normalize。常见的normalize为 `(data - mean)/std`使用numpy 很简单：`(x-x.mean())/x.std()`


为什么要添加activation：这是因为activation得出的结果与true label的error可以衡量与ture label 的距离相差多少。而使用threshold后的结果，则不能凸显与之程度。在这一章中使用的是linear activation，即直接输出，没有变化。

神经元的数据流动过程
1. net input ：`net_input = np.dot(X,self.w_[1:]) + self.w_[0]`. self.w_[0] is the bias
2. activation function
3. compute the error : `error = true_labe - output`. here output came from activation. But if the perceptron nueron, then it is the final predicted label
4. get the predicted label by comparing the threshold
5. do the next example or next batch


# Chapter 3 : A Tour of Machine Learning Classifiers Using scikit-learn

The five main steps that are involved in training a supervised machine learning algorithm can be summarized as follows:
1. Selecting features and collecting labeled training examples.
2. Choosing a performance metric.
3. Choosing a classifier and optimization algorithm.
4. Evaluating the performance of the model.
5. Tuning the algorithm.

在这一章中介绍了sklearn 中几个常用的分类算法：logic regression，svm，decision tree, random forest, KNN(k nearest neighbours)。

解决的疑惑有：
1. logic regresssion ：sigmoid activation function的由来。
2. 各算法的优缺点。后面详细说明
3. decision tree 判定node的计算过程：通过IG(information gain)来决定对哪个feature 进行分割
4. random forest 是在整个dataset 中随机有放回的选择一些examples，然后再随机选择一些features来build decision tree。而 sklearn中默认情况下是直接使用全部的dataset，然后随机选择subfeature来进行build tree组成random forest。
5. overfitting and underfitting
6. regularization

## 概率，发生比(odds), 净输入(net_input) 和logit 公式之间的联系

首先明确几个感念，具体细节可参考[csdn](https://blog.csdn.net/youyanyu3817/article/details/106279297)：
- 概率：概率是指一个事件发生的可能性
- 发生比:发生比指一件事发生与其不发生的概率之比，
- Logit :Logit是给发生比取对数，即log(Odds)，其中log是自然对数。
- net_input = X\*w

因为我们从上一章中了解到了神经元中的信息流动过程。知道了net_input。在得到net_input之前的过程基本都是一致的。那么在perceptron中直接使用net_input 经过threshold的结果（label）作为error 的参数 ·`error = true_labe - label`；而在Ada 中则是使用linear activation function。即直接使用net_input来计算error:`error = true_label - net_input`。

由于最后一种神经元采取的是概率作为评判结果。因此对于二分类模型来说，结果在[0,1]之间。因此我们要使net_input经过activation function 之后结果也在[0,1]之间。

那么为什么会引入发生比和logit呢？我想到的是这两者与概率有关。第一位吃螃蟹的人尝试了以下方法，觉得不错。记住我们的目的是要求概率，且与net_input扯上关系。

- 可以让 `p = net_input` 吗？

不可以，因为这样的话net_input的范围只能控制在[0,1]，不方便update weights。太窄了。

- 可以让`odds = net_input` 吗？

不可以，因为发生比有不对称性, 当概率从0.1增加到0.2，时，发生比增加了0.139；当概率从0.8增加到0.9时，发生比却增加了5，虽然概率的增量相同，但发生比的增量却大大的不同。其他例子可以参考[csdn](https://blog.csdn.net/youyanyu3817/article/details/106279297) 

- 可以让`logit = net_input` 吗？

为了解决odds的不对称性，引入了logit。因为log函数容易计算和求导。因此让`logit(p) = z`, here z = net_input = X\*w.

log(p/1-p) = z
- p/(1-p) = e^z
- p = e^z/(1+e^z)
- p = 1/(1+e^-z)

到这里我们把概率和net_input联系在了一起，并且`1/(1+e^-z)`就是activation function。

## 通过activation function 更新updates

![ada and logic regression difference](/assets/images/python_ml/ada_lr_nueron.png)

从图中可以看出 ada 与 logic regression 之间的不同之处：
1. activation function是不同的。ada 是linear activation；logic regresion是sigmoid activation。
2. logic regression 可以求得当前class label 的概率。

logic regression 的cost function是求得最大的likelihood. 求deta_w的过程如下：
1. 获得最大likelihood，这是一个乘积公式
2. 求log转化为求和公式

为什么转化为求和公式：
1. 因为乘积会导致数据溢出。
2. 求导方便

## overfitting and underfitting

 If a model suffers from overfitting, we also say that the model has a high variance, 

Similarly, our model can also suffer from underfitting (high bias), which means that our model is not complex enough to capture the pattern in the training data well and therefore also suffers from low performance
on unseen data.

overfitting : high variance
underfitting : high bias

## regularization

The concept behind regularization is to introduce additional information (bias) to penalize extreme parameter (weight) values. 

The most common form of regularization is so-called L2 regularization (sometimes also called L2 shrinkage or weight decay), which can be written as follows:

![r2 regularization](/assets/images/python_ml/r2_regularization.png)

![r2 regularization](/assets/images/python_ml/cost_lr.png)

1. 之所以叫做weight decay 是因为偏移量与weight 有关。
2. 同时为了做regularization也要进行feature normalization，这样各weights之间有可比较性。

## information gain in Decision Tree

1. Gini impurity
2. Entropy
3. Classification error

## 


