---
layout: default
title: python 知识点
date:   2021-09-12 20:28:05 -0400
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

## Python

### [set](https://www.w3schools.com/python/python_sets_methods.asp)



### [defaultdict](https://docs.python.org/3/library/collections.html#collections.defaultdict)

```python
from collections import defaultdict
dic_count = defaultdict(int) # 用于计数, default value is 0
dic_list = defaultdict(list) # 用于储存数组, default value is [],a empty list
dic_labmda = defaultdict(lambda : 'ok')
 # default value is the return value in the lambda, here is 'ok', 
# you can also set other values such as empty string, float('inf')

```

两个defaultdict在一起使用需要使用lambda，例如：
`table_food = defaultdict( lambda : defaultdict(int) )`，如果不使用lambda，`defaultdict(defaultdict(int))`是会报错的。例如[1418. 点菜展示表](https://leetcode-cn.com/problems/display-table-of-food-orders-in-a-restaurant/)

---
### [bisect](https://docs.python.org/3/library/bisect.html)

`bisect.bisect_left(stack,idx)`：通过2分法排序查找idx 在stack中位置（从左边开始），若`bisect.bisect_right(stack,idx)`则是从右边开始数。
```python
import bisect

a = [1,2,3,4,5]

bisect.bisect_left(a,3)
# output : 2

bisect.bisect_right(a,3)
# output : 3

```

### [sort OR sorted](https://stackoverflow.com/questions/4233476/sort-a-list-by-multiple-attributes/4233482)

```python
a = ['ok','as','apple','pear','desk']
b = sorted([(word,len(word)) for word in a], key=lambda x: (-x[1],x[0]))
 # output of b :[('apple', 5), ('desk', 4), ('pear', 4), ('as', 2), ('ok', 2)]
```
>**Note:**通过 _key_ 对关键字进行排序，首先是单词中字符的数量x[1],之所以加`-` 是因为想让字符数量从大到小排列，而相同字符数量的单词，则是按照单词在单词字典中的顺序排列。例如abe 要排在 abm 之前。

### [range](https://docs.python.org/3/library/stdtypes.html#typesseq-range)

```python
for i in range(3,0,-1):
    print(i)
    # output : 3,2,1

for i in range(0,10,2):
    print(i)
    #output : 0,2,4,6,8

```

### ord

- `ord("A") -> 65` :字符转化为ASCII码（十进制）
- `chr(65) -> 'A'`: ASCII 转化为字符

查看[ASCII Table](https://www.asciitable.com/)

![ASCII table](/assets/images/python/ascii.png)

### [heapq](https://docs.python.org/3/library/heapq.html)

堆堆数组进行heapify后可以保证数组index=0为最小的值，因此python自带的heapq属于小顶堆。每次`heappop, heappush` 都会改变数组中数值的排列。

如果想要大顶堆，可以在每个数组中的数前加上`-`号，提取出来时再加上`-`即可。

```python
import heapq

ls = list([1, 3, 5, 7, 9, 2, 4, 6, 8, 0])

heapq.heapify(ls) # 堆化，此时ls的element的顺序发生变化，排在ls[0] 是ls中最小的一个数
ls 
 # output : [0, 1, 2, 6, 3, 5, 4, 7, 8, 9]

heapq.heappop(ls) # 弹出最小的数，此时ls中的数据也发生变化
ls
 # output: [1, 3, 2, 6, 9, 5, 4, 7, 8]

heapq.heappush(ls,10) # 往堆中加入数据
ls
 # output: [1, 3, 2, 6, 9, 5, 4, 7, 8, 10]

```
### [deque](https://docs.python.org/3/library/collections.html#collections.deque)

python的deque 函数可以行使queue的功能：first in，first out。并且deque可以在两端进行插入数组和排除数据。

```python
from collections import deque

q = deque([1,2,3,4,5])

q.append(0) # 默认增加在数组最右端
q
 # output:deque([1, 2, 3, 4, 5, 0])

q.appendleft(9) # 从左端加入
q
 # output: deque([9, 1, 2, 3, 4, 5, 0])

q.pop() # 默认从右端排出
 # output : 0

q.popleft() # 从左端排出
 # output : 9

```

### [one line equation](https://stackoverflow.com/questions/17321138/one-line-list-comprehension-if-else-variants)

- 如果只有一个if，可以这样写`[x for x in range(10) if x%2 == 0]`
- 如果既有if又有else，可以这样写`[x if x%2==0 else x*10 for x in range(10)]`

---

## Pandas.DataFrame

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

---

## Numpy

### [numpy.argmax(a, axis=None, out=None)](https://numpy.org/doc/stable/reference/generated/numpy.argmax.html)

Returns the indices of the maximum values along an axis.
返回指定axis最大值的indices.如果是二维matrix，我们想求每一行中的最大值的indice，我们可以这样写`indexs = np.argmax(circle_matrix,axis=1)`


## re

python使用Regular expression operations。需使用re package。
```python
import re
```
- 一般我们使用`r'pattern'`来表示regular expression 中的pattern。其目的是可以防止`\`带来的困扰。例如想查找string中是否存在`\\`，则pattern需要这样写：`\\\\` or `r'\\'`。因此使用r 可以让pattern更容易些，也更容易理解，引号里面是什么就是查找什么内容。不需要对特殊字符进行转化。

- `\w`: For Unicode (str) patterns, like [a-zA-Z0-9_].表示所有的小写字母，大写字母，0到9的数字和下划线。

### re 的使用

re 的使用有两种方式：

1. 使用compile方式

```python
prog = re.compile(pattern)
result = prog.match(string)
```
首先使用re.compile(pattern)生成regular expresssion object。然后再使用re的function，like match(), search()

2. 直接使用re的function。function中要有pattern和string。

```python
result = re.match(pattern, string)
```

这两种方式的结果是相同的。但是如果多次使用regular expression。则使用re.compile 的效率更高。



