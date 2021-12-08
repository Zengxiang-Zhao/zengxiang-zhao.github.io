---
layout: default
title: sklearn
parent: books
date:   2021-11-10 20:28:05 -0400
categories: sklearn
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

## Load datasets

```python
from sklearn import datasets

iris = datasets.load_iris()
X = iris.data
y = iris.target
```

关于sklearn中的datasets还有哪些，可以参考[官方档案](https://scikit-learn.org/stable/datasets.html#datasets)

## [train_test_split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html?highlight=train_test#sklearn.model_selection.train_test_split)

对data 进行划分，自带stratify功能

```python
from sklearn.model_selection import train_test_split

X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.3,random_state=1, stratify=y)
```

stratify 的目的是使分割后的train，test中的数据都具有相同的比例。shuffle default is True。

## StandardScaler

对数据进行normalization

```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
sc.fit(X_train)
X_train_std = sc.transform(X_train)
X_test_std = sc.transform(X_test)
```

使用sklearn自带的StandardScaler对数据进行noramalizatio。首先使用fit获得mean 和 variance。然后再使用transform把想要转化的数据转化。


