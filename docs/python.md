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

## install packages

### conda

```bash
conda install package_name=version

conda install 'package_name>= version'

conda install 'package_name<= version'
```

> If any of these characters, '>', '<', '|' or '\*', are used, a single or double quotes must be used



## Python

### [使用dateutil 对string转换成datetime](https://dateutil.readthedocs.io/en/stable/parser.html)

```python
from dateutil import parser
import pandas as pd

s = '7/1/2021 6:29'
parser.parse(s)
# output: datetime.datetime(2021, 7, 1, 6, 29)

parser.parse(s).strftime('%m/%d/%Y')
# output: '07/01/2021'

# calculate the TAT. the endDate and startDate are datetime style
tat = round((endDate-startDate)/pd.Timedelta(hours=24), 1)

# convert the datetime to string
timeStamp = datetime.datetime.now().strftime('%Y-%m-%dT%H:%M:%S')
#output: '2025-01-21T02:33:59'
```

Now you can use the result get what kind of format you'd like to convert. This package is easy and friendly. It just convert the string to datetime directly.

### [使用yml格式作为config 文件格式](https://circleci.com/blog/what-is-yaml-a-beginner-s-guide/#:~:text=YAML%20is%20a%20digestible%20data,that%20JSON%20can%20and%20more.)

ymal : "Yet Another Makeup Language"

1. install: `pip install PyYAML`
2. import : `import yaml`
3. load : `with open('config.yml','r') as f: config = ymal.safe_load(f)`
5. dump : 'with open('config.yml','w') as f: config = ymal.safe_dump(f)'

### use shutil to copy files

When I use VSCode to link the remote server and run code in VSCode. The code `shutil.copy(path_src,path_dest)` will show errors that `Permission Deny`. Anyway you can't copy files to some directory.
如果换了文件夹，则可以成功复制。后面使用`shutil.copyfile(file_src,file_dest)`则没有问题。

### [use `pipreqs` to extract the packages used in the script](https://stackoverflow.com/questions/58737741/how-to-list-all-python-packages-used-by-a-script-in-python3)

```bash
pip3 install pipreqs

pipreqs ./your_script_directory

```

### use glob to search specific files in specific depth

If you don't care about the depth of the dictionary, then you can use `pathlib.Path.rglob(pattern)` to search the files. 
But the problem is that when the file or path has a long name, this method will generate error. 
Another method is to use glob to search the files with a specific depth in the dictionary. Show as below.

```python
from pathlib import Path
import os,glob

pattern_file = r'Chip Loading*.xlsm'
path_folder = Path(/mnt/hgsf/test)
list_desiredFiles = path_folder.rglob(pattern_file)

# another method
#zero depth
list_desiredFiles_d0 = glob.glob(os.path.join(path_folder,pattern))
list_desiredFiles_d1 = glob.glob(os.path.join(path_folder,'*',pattern))
list_desiredFiles_d2 = glob.glob(os.path.join(path_folder,'*','*' ,pattern)) # how many star * means how many depths of the dictionary

```

### use * to unpack the list

```python

for a in zip([[1,2,3],[4,5,6]]):
    print(a)

# output 
#([1, 2, 3],)
#([4, 5, 6],)

for a in zip(*[[1,2,3],[4,5,6]]): # add * to unpack a list
    print(a)
    
# output
#(1, 4)
#(2, 5)
#(3, 6)


```

### format string

参考[realpython](https://realpython.com/python-formatted-output/)

```python
score = 0.7777777
print('The score is %.3f'%score) # output : The score is 0.778

```

### [print space](https://stackoverflow.com/questions/23776824/what-is-the-meaning-of-s-in-a-printf-format-string/23777065)

```python
for f in range(X_train.shape[1]):
    print("%2d) %-*s %f"% (f+1, 30,
                           feat_labels[indices[f]],
                           importances[indices[f]]))
```

以上代码中，
- `%2d)`  对应的是`1)`, 
- `%-*` 对应的是30个space，这里的`*`对应30，前面的`负号(-)`表示是在文字后使用空格填补30个字符，如果不加负号则是在文字前面填补空格到30个字符。
- `s` 对应的是`feat_labels[indices[f]]`
- `%f` 对应的是`importances[indices[f]]`

![string format with space](/assets/images/python/string_format.png)

以下是string 没有添加空格后的效果

![string format with space](/assets/images/python/string_no_format.png)

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

## Numpy

### numpy axis

[这篇文章](https://www.sharpsightlabs.com/blog/numpy-axes-explained/) 对numpy 的 axis解释的不错。直观的看下图：

![numpy axis](/assets/images/python/np_axis.png)

### [numpy.array_equal](https://numpy.org/doc/stable/reference/generated/numpy.array_equal.html)

判断两个numpy array是否相同。只返回一个bool value， True or False。而不是一个bool matrix。

### [numpy.argmax(a, axis=None, out=None)](https://numpy.org/doc/stable/reference/generated/numpy.argmax.html)

Returns the indices of the maximum values along an axis.
返回指定axis最大值的indices.如果是二维matrix，我们想求每一行中的最大值的indice，我们可以这样写`indexs = np.argmax(circle_matrix,axis=1)`

### [numpy.random.normal(loc=0.0, scale=1.0, size=None)](https://numpy.org/doc/stable/reference/random/generated/numpy.random.normal.html)

可以用于对weights的初始化。

Parameters:
- loc : mean
- scale : std
- size : number of data

### [numpy.ravel(a, order='C')]

Return a contiguous flattened array.

```python
x = np.array([[1, 2, 3], [4, 5, 6]])
np.ravel(x)
# output : array([1, 2, 3, 4, 5, 6])
```

### [numpy.dot(a, b, out=None)](https://numpy.org/doc/stable/reference/generated/numpy.dot.html?highlight=numpy%20dot#numpy.dot)

两个matrix进行相乘。
1. 如果a,b 是一维的数组，则是进行点乘。
2. 如果a,b 是多维数组，则推荐使用 `matmul or a @ `. here @ calls np.matmul . 这是因为在处理大的matrix时，后两者的处理速度要比numpy.dot 更快。参考[stack overflow](https://stackoverflow.com/questions/52062496/why-is-a-dotb-faster-than-ab-although-numpy-recommends-ab)



### [numpy.matmul](https://numpy.org/doc/stable/reference/generated/numpy.matmul.html#numpy.matmul)

多维数组进行相乘。不能是scaler value。

shape that matches the signature (n,k),(k,m)->(n,m).

### np.vstack OR np.hstack

[参考官方文档](https://numpy.org/doc/stable/reference/generated/numpy.vstack.html)

```python
X_combined_std = np.vstack((X_train_std,X_test_std))
y_combined = np.hstack((y_train,y_test))
```

- np.vstack : 一行一行的叠加, vertically (row wise)
- np.hstack : 一列一列的叠加, horizontally (column wise).

### [numpy.where](https://numpy.org/doc/stable/reference/generated/numpy.where.html)

`numpy.where(condition[, x, y])`

如果condition 返回True，则返回x，otherwise,return y.

```python
a = np.arange(10)
np.where(a < 5, a, 10*a)
```

### [np.argsort(a)](https://numpy.org/doc/stable/reference/generated/numpy.argsort.html)

Returns the indices that would sort an array.对array a 进行从小到大排序。

```python
a = np.array([0,2,1,4,3,5])

indices = np.argsort(a)[::-1] # 从大到小进行排序
```

### [numpy.cumsum(array)](https://numpy.org/doc/stable/reference/generated/numpy.cumsum.html)

Return the cumulative sum of the elements along a given axis.相当于前缀和

### [numpy.linspace](https://numpy.org/doc/stable/reference/generated/numpy.linspace.html)

`numpy.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None, axis=0)`

把从start 和stop中间的距离进行分割成num份

---

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

### 匹配多种可能性

使用`()`和`|`进行组合匹配多种可能性。如下，reports后面加上？表示s可有可无。`(is|are|were|was)` 表示这个位置有四种可能性的词。因为使用了`()`来寻找pattern，因此可以使用output.group(0)表示匹配到的句子或可能的内容。 `.*?` : 非贪婪匹配。
```python
output = re.search(r'Tax reports? (is|are|were|was) reported .*?(\n|\.)')
output.group(0)
```


## openpyxl

### 给excel 中的cell填充颜色
from openpyxl.styles import PatternFill

```python
def color_ws_reportDate(ws,column='M'):
    """Color the report date in ws : reported : green , unreported: red
    - ws : worksheet opened by openpyxl
    """
    fillGreen = PatternFill(fill_type='solid',
              start_color='6BCB77',
              end_color='6BCB77')

    fillRed = PatternFill(fill_type='solid',
              start_color='FF6B6B',
              end_color='FF6B6B')
    for cell in ws[column]:
        try:
            value = str(cell.value).strip()
            if value and value != 'nan':
                print(f'value is : {value}')
                cell.fill=fillGreen
            else:
                cell.fill = fillRed
        except:
            message(f'Error when color report date in cell contain: {cell.value}',bcolors.FAIL)
            
```


