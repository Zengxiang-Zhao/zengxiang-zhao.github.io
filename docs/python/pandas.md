---
layout: default
title: pandas
parent: python
date:   2021-11-10 20:28:05 -0400
categories: python
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

## Pandas.DataFrame

### [pd['column_name'].astype(bool)](https://stackoverflow.com/questions/29314033/drop-rows-containing-empty-cells-from-a-pandas-dataframe)

有时需要根据某一列中的值删除列。例如 Id 列中有 empty string ‘’，那么使用 `df.dropna(subset='Id')` 并不管用，此时可以使用 ·pd[pd['Id'].astype(bool)]· 来删除不想要的rows。

### [pandas.DataFrame.sort_values](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html)

可以对dataframe 的某一列进行排序。当对查看某个dataframe中的缺失值时非常有用，例如:
```python
pd.options.display.min_rows = 80 # 强行让pd显示80行

train_data.isna().sum().sort_values(ascending=False) # 排列缺失值

```

### [pandas.DataFrame.values](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.values.html)

返回numpy 值，不过官方推荐使用[DataFrame.to_numpy()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_numpy.html#pandas.DataFrame.to_numpy)

例如在画图时可以直接提取一column 

```python
biochem1_ref_1 = df_biochem1['ref_1'].values
# covert dataframe to numpy matrix
biochem1_first_circle_matrix = df_biochem1[['A_1','C_1','G_1','T_1']].to_numpy()
```

### [pandas.concat](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html)

pandas.concat(objs, axis=0, join='outer', ignore_index=False, keys=None, levels=None, names=None, verify_integrity=False, sort=False, copy=True)

objs: a sequence or mapping of Series or DataFrame objects

例如
```python
pd.concat([s1, s2])
```

### [df.isnull](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.isnull.html)

df.isnull() 会查看每个cell中是否存在missing value。如果是NaN则返回True，otherwise return False。然后可以使用`df.isnull().sum()`对每一列的missing value 求和。

`df.isnull().sum()` : This way, we can count the number of missing values per column.

### [df.values](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.values.html)

Return a Numpy representation of the DataFrame.

sklearn 中主要是对numpy的数据进行处理。现在sklearn中很多function都可以支持pandas。所以如果可以转化为numpy则尽量转化为numpy后再使用sklearn。

### [Series.map(arg, na_action=None)[source]](https://pandas.pydata.org/docs/reference/api/pandas.Series.map.html)

使用map对dataframe中的数据进行转化。例如将categorical values转化为interger values。

arg : a dictionary 

```python
size_mapping = {
    'XL':3,
    'L':2,
    'M':1
}
df['size'] = df['size'].map(size_mapping)
```

### [pd.iloc[?]](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iloc.html)

使用pd.iloc可以像numpy那样对dataframe进行分割。starting from 0

```python
pd.iloc[:,1:] # 第二列之后的所有列

pd.iloc[:,0] # 第1列
```

### [df.isin :Select rows based on column that in or not in the value list](https://www.codegrepper.com/code-examples/python/pandas+dataframe+select+rows+not+in+list)

```python
#filter dataframe with list that the value in the list
df_runID_results_dropna = df_runID_results_dropna[df_runID_results_dropna['idType'].str.upper().isin(check_idTypes)] # check_idTypes is a list like['A','B','C']

# filter dataframe with list that the value not in the list
df_runID_results_dropna = df_runID_results_dropna[~df_runID_results_dropna['caseNo'].str.upper().isin(remove_caseNo)]
```

---
