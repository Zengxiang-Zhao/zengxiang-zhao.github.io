---
layout: default
title: Linux CLI
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 100

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

## [Customizing your terminal using OhMyZsh](https://gabrieltanner.org/blog/customizing-terminal-using-ohmyzsh)
此链接解释怎么在terminal 配置ohmyzsh shell.

如果是在Ubuntu中进行修改则需要注意以下几点：
  1. [安装OhMyZsh](https://ohmyz.sh/)，在ternimal输入 : `$ sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"`
  2. 更改 zsh-syntax-highlighting的背景颜色，不然在Dark solarized的背景下看不清：在 `~/.zshrc`中添加 `ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=yellow"`


## [vim 配置](https://blog.csdn.net/qingdujun/article/details/81411197)

我的vim配置如下：
```bash
set number
set nocompatible

" hight the syntax
syntax on

set showmode
set autoindent
set smartindent
set expandtab
set tabstop=4
set shiftwidth=4
set softtabstop=4

set showmatch
set hlsearch
set incsearch
set ignorecase

" 自动补全括号
inoremap ' ''<ESC>i
inoremap " ""<ESC>i
inoremap ( ()<ESC>i
inoremap [ []<ESC>i

```

## mkdir

1. `mkdir -p d1/d2/d3 `一次生成多个folder，即使d2,d3不存在，同样可以建立.


## tar

1. `tar  -xf v5.24.1.tar.gz --strip 1 "*/data" "*/environment.yaml"`

`-x` 表示解压， `-f` 表示文件，`--strip 1`表示不重建前1层目录。这一句表示从`v5.24.1.tar.gz` 中提取出`data, environment.yaml`文件，详细的关于tar的解释可见[What does --strip-components -C mean in tar?](https://unix.stackexchange.com/questions/535772/what-does-strip-components-c-mean-in-tar)

`-c`表示`Create a new archive containing the specified items.`，例如使用tar压缩文件时，则可以使用`tar -cf archive.tar a/b/c/FILE`，`a/b/c/FILE` 为想要压缩的文件，`archive.tar`为压缩后的文件名。

列出tar文件中的内容，可以使用`tar -tf archive.tar`

- `tar  -xf v5.24.1.tar.gz` : 解压文件
- `tar -cf archive.tar a/b/c/FILE` ： 压缩文件
- `tar -tf archive.tar` ： 列出tar中的文件

## [find](https://opensource.com/article/18/4/how-use-find-linux)

找到满足一定要求的文件

```bash
find 文件路径 -type f -name 'pattern'
```

- find 后面的第一个参数是文件路径，想从哪一个文件夹中查找文件
- `-type`: 想要查找的类型, `f`: file ; `d`: directory
- `-name`: 想要查找的名称，使用引号括起来。例如想查找python 文件，以 py 结尾的文件。则可以使用"\*.py"

如果后缀中有大小写，那么可以使用`-iname`，表示case-insensitive。

```bash
find 文件路径 -type f -iname '*.jpg'
```

如果想查找多个后缀名称的文件，可以使用or 把多个要查找的文件pattern结合在一起。如下：

```bash
find 文件路径 -type f \( -name '*py' -o -name '*ipynb' \)
```

> 要把多个条件放在括号中。其中前后两端要留有空格，使用反斜杠进行标记。

- `-size`: 查找的文件打下

```bash
k       kilobytes (1024 bytes)
M       megabytes (1024 kilobytes)
G       gigabytes (1024 megabytes)
T       terabytes (1024 gigabytes)
P       petabytes (1024 terabytes)
```

例如查找当前文件夹下文件大小超过1M的文件

```bash
find . -size +1M

find . -size -1M
```
> `+1M`中的+号表明文件大于1M。如果是想查看小于1M的则可以使用`-` 号。


## [awk 的使用](https://linuxize.com/post/awk-command/)

`awk` is a command to process the output of other command, and then extract the interested content.

```bash
cat failed_vcf.txt| awk -F '/' '$1 ~ /09ab/ {print $NF}' | xargs -d '\n' -I{} touch {}
```

1. `-F` define the delimer, here is `/`
2. `$NF` means the last collumn
3. `$1 ~ /09ab/` : regex that the first collum need to contain `09ab`

## download multiple illumina project data through `xargs`

```bash
cat download-list.txt | xargs -d '\n'  -I{} /home/user/Softwares/linux-amd64/icav2 projectdata download {}
```
1. `download-list.txt` contains the folder Id or file ID that you can find in the illumina ICA
2. [`xargs`](https://linuxhint.com/xargs_linux/) can be used to redirect the arguments to other command. Here `-d` define the delimer; `-I{}` define a variable named `{}` that can be used in other command 
3. `/home/user/Softwares/linux-amd64/icav2`: specify the absolute path to the command, if you just use the `icav2`, it cannot work. Even though you use alias and operate the command in bash terminal.


