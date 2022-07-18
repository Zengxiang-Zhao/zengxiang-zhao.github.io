---
layout: default
title: numpy 知识点
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
