---
layout: default
title: 画图的各种知识点
date:   2021-10-31 20:28:05 -0400
categories: pyhon
nav_order: 97

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

## import packages

```python
import pandas as pd
import numpy as np
import seaborn as sns

import matplotlib.pyplot as plt
%matplotlib inline
```

## plot graph

### [seaborn.countplot](https://seaborn.pydata.org/generated/seaborn.countplot.html)

`seaborn.countplot(*, x=None, y=None, hue=None, data=None, order=None, hue_order=None, orient=None, color=None, palette=None, saturation=0.75, dodge=True, ax=None, **kwargs)`

seaborn 可以直接把dataframe作为输入`sns.countplot(data=df_data,x='class',y='rate')`。x与y都是df_data中的column name。可以参考官方的画图。同时可以使用matplotlib对图进行操作，因为seaborn是基于matplotlib开发的。

```python
sns.countplot(df_data.iloc[index]['class'])
axis = plt.gca() # 获得当前的axis
axis.set_title('class distribution') # 添加标题
axis.set_xlabel('Class') # 添加xlable
axis.set_ylabel('Count')
# axis.set_xlim([0.5,0.9]) # 也可以设置xlim和ylim
plt.show()
```







