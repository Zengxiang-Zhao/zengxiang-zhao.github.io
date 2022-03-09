---
layout: default
title: 杂项
date:   2021-10-3 20:28:05 -0400
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

# 更改GitHub personal token

## [创建新的Personal Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

## 在电脑上更新

1. 使用 `command+ 空格` 搜索 Keychain Access
2. 在弹出界面中搜索`github`
3. 在github login 这一行选中，右键，删除。
4. 在Terminal端进行push。填入Username ， 在Password选项粘贴personal Token。
5. 完成


# 引用本地link

图可参考[python知识点](/docs/plot_graph#pltbar)

直接以`/docs/...`开头，后面如果不确定可以在web中点开查看后面内容。


# 写公式

`<img src="https://render.githubusercontent.com/render/math?math=\| w \|_2^2 = \sum_{j=1}^{m}w_j^2
">`

在math后面写你想写的公式。如果有些符号不确定可参考[Latext pdf 文档](https://www.caam.rice.edu/~heinken/latex/symbols.pdf)

## 在公式中写加号+ 

在GitHub markdown 的公式中填写加号+，使用`%2B`。

<img src="https://render.githubusercontent.com/render/math?math=F1 = 2 \frac{PRE \times REC}{PRE %2B REC}
">

## [install nodejs from source file](https://stackoverflow.com/questions/63312642/how-to-install-node-tar-xz-file-in-linux)

```bash
sudo cp -r node-v14.15.5-linux-x64/{bin,include,lib,share} /usr/
```
其中核心思想就是把想要安装的package中的bin,include,lib,share 文件夹中的内容放到你当前env下的相同文件夹中。可使用 ·which node·来查看当前node安装的路径。
然后把 /usr/ 换成env下的路径。此方法可以延伸到手动安装其他的package
