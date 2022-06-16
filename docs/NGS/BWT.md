---
layout: default
title: Burrows–Wheeler transform(BWT)
parent: NGS
date:   2021-11-10 20:28:05 -0400
categories: NGS
---


---

# 什么是BWT

[维基百科-BWT](https://en.wikipedia.org/wiki/Burrows%E2%80%93Wheeler_transform#:~:text=The%20Burrows%E2%80%93Wheeler%20transform%20is,Center%20in%20Palo%20Alto%2C%20California.)中有详细的解释。

BWT是将一长串字符进行所有可能的转换，然后取最后一列。

# BWT是如何转换的

## Rearrange, sort, choose last column : 详见Wikipedia 里面的Transformation table

## Get the original string through last column string: 详见Wikipedia里面的Inverse transformation table

# BWT为什么能够帮助压缩字符

Wikipedia里面举了一个例子。一长串字符里面有`the` 这个单词。当the这个词经过转换放在最后面的时候，再经过两次转换则，在长串字符的最前面的两个字符是`he`。那么此时最后一个字符是`t`。那么在sort的时候，就会把`he`开头的字符串放在一起。那么取最后一列时很多的`t`也就放在一起了。

# BWT在生物信息学中的作用

使用BWT对全基因组生成index，方便后续reads进行查找比对。详细的还需要进一步研究： suffix tree
可以参考[这篇文章](http://www.bioinfo.online/html_article_20222618975.html)

