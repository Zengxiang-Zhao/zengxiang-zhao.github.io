---
layout: post
title:  "Titanic Disaster"
date:   2021-09-20 20:28:05 -0400
categories: jekyll update
---

# Titanic Disaster 二分类问题


## 3. Model Selection and Parameters Tuning

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
### 1. 可以使用 sklearn 中的metrics 中的function来对二分类的模型进行评估

 

``` python
   from sklearn import metrics
   def get_scores(y_preds,y):
       return {
            "Accuracy":metrics.accuracy_score(y_preds,y),
            'Precision':metrics.precision_score(y_preds,y),
            'Recall':metrics.recall_score(y_preds,y),
            'F1':metrics.f1_score(y_preds,y),
            'ROC_AUC':metrics.roc_auc_score(y_preds,y)
        }
```

{% highlight python %}
from sklearn import metrics
   def get_scores(y_preds,y):
       return {
            "Accuracy":metrics.accuracy_score(y_preds,y),
            'Precision':metrics.precision_score(y_preds,y),
            'Recall':metrics.recall_score(y_preds,y),
            'F1':metrics.f1_score(y_preds,y),
            'ROC_AUC':metrics.roc_auc_score(y_preds,y)
        }
{% endhighlight %}

### 2.  使用sklearn.model_selection 中的 train_test_split 对数据进行分割，返回值是 train-test 对
> **Note:** sklearn.model_selection.train_test_split(*arrays, test_size=None, train_size=None, random_state=None, shuffle=True, stratify=None)[]
> Returns: spliting list, length = 2*len(arrays)
> List containing train-test split of inputs.
> [source](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html?highlight=train_test_split#sklearn.model_selection.train_test_split)

### 3.  模型的选择：用于二分类的模型有很多，最后可以将这些已经训练好的模型进行`整合（ensemble）`，评估所有模型的结果后得出 `最终` 结果。

```python
from sklearn.tree import DecisionTreeClassifier
from xgboost import XGBClassifier
from lightgbm import LGBMClassifier
from sklearn.linear_model import LogisticRegression
from sklearn import svm
from catboost import CatBoostClassifier
from sklearn.ensemble import AdaBoostClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.neighbors import KNeighborsClassifier
from sklearn.ensemble import RandomForesClassifier,VotingClassifier
```

### 4. 使用function `一次性训练` 多个模型。

```python
def  train_model(model):
    model_ = model
    model_.fit(X_train,y_train)
    y_preds = model_.predict(X_val)
    return get_scores(y_preds,y_val)

model_list = [
    DecisionTreeClassifier(random_state=42),
    RandomForestClassifier(random_state=42),
    XGBClassifier(random_state=42),
    LGBMClassifier(random_state=42, is_unbalanced=True),
    svm.SVC(random_state=42,verbose=0),
    AdaBoostClassifier(random_state=42)
]

model_names = [
    'Decision Tree', 'Random Forest', 'XGBoost', 'Light GBM', 'Logistic Reg', 'SVM', 'CatBoost', 'AdaBoost'
    ]

scores = pd.DataFrame(columns=['Name', 'Accuracy', 'Precision', 'Recall', 'F1', 'ROC_AUC'])

for i in range(len(model_list)):
    score = train_model(model_list[i]) # 训练第i个模型，并返回score
    scores.loc[i] = [model_name[i]] + list(score.values()) # 把第i个模型的结果放到DataFrame中的第i行中
```
> **Note:** train_model 函数中的fit 和 predict 可以换成自己写的函数，然后根据model_list中的model来进行训练模型和预测分数。并把分数储存到DataFrame中，方便后面生成图。
### 5.使用matplotlib.pyplot 展示模型效果。


