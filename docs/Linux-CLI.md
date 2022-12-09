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

## [How to parse bash script arguments](https://www.shellscript.sh/tips/getopt/index.html)

- use `getopt`
- [Resouce 2](https://stackabuse.com/how-to-parse-command-line-arguments-in-bash/)


## 如何在Linux系统中管理用户并赋予相应的权限

如果我们写了一些程序想让别人运行，但是并不像让他们对文件进行操作，例如查看，修改。只是让他们运行文件而已。

1. Create a "Group". If you use your account, then when you created your account, a group would also be created with the same name as your username. e.g "appUser"
2. Creat a new user `adduser --ingroup appUser testUser`
3. Use `ls -alh` to show the status of the file. e.g `-rwxr-xr-x 1 user group  21K Oct 21 14:54 utils.py`. Here `-rwxr-xr-x`:
  - if this is a dictionary, then the first letter will be `d`
  - The following three letters(rwx) means the permission of the owner: r(read - 4), w(write - 2), x(executable - 1)
  - The follwoing three letters(r-x) means the permission of the same group has only read and executable permission
  - The following three letters(r-x) means the permission of other users who don't belong to the group will have the permission : read and executable
4. [Change the permission of the folders and files](https://www.pluralsight.com/blog/it-ops/linux-file-permissions#:~:text=To%20change%20directory%20permissions%20for,only%20read%20permission%20for%20everyone.) : `chmod 711 folder` OR `chmod 755 scriptFile`
   - if the folder has the permission 'x' for group, then the group users could use `cd` to go into that folder
   - if the folder has the grouup has 'r' permission, then the group users could use `ls` to show the content in that folder]
   - if you'd like the user use some script, then you need to give the parent folders at least 'x' permission to the group. The parent folders should possess the same permission as the child folders at least.
   - e.g. `drwxr-xr-x` : the group users could use `cd` and `ls`
   - e.g. `drwx--xr-x` : the group users could only use `cd`, cannot use `ls`
5. put the executable scripts in an separate folder. 
6. Do some test to confirm your process is ok


## [Manage a group and user in linux](https://linuxize.com/post/how-to-create-groups-in-linux/)

- show groups : `cat /etc/group`
- show users : `cat /etc/passwd`
- add a group : `sudo groupadd appUser`
- [delete a group](https://linuxize.com/post/how-to-delete-group-in-linux/) : `sudo groupdel appUser`
- display who is a member of a group : `getent group group-name`
- [add a group to a user](https://www.pluralsight.com/blog/tutorials/linux-add-user-command?exp=3) : `sudo usermod -a -G appUser testuser`. You need to login in a user that has the sudo permission.
- [create a new user to a group](https://careerkarma.com/blog/linux-add-user-to-group/#:~:text=You%20can%20add%20a%20user,user%20and%20the%20user's%20username.) : `sudo useradd -g primary_group -G another_group -s /usr/bin/bash -m new_user`. Here `-m` means create a homde directory for new user; `-s` means using specific shell.
- [change passwd for new user](https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command/): `sudo passwd new_user`
- [delete a user](https://linuxize.com/post/how-to-delete-users-in-linux-using-the-userdel-command/) : `userdel username`


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

## How to find all files containing specific text (string) on Linux

```bash
grep -rnw '/path/to/somewhere/' -e 'pattern'
```

1. `-r` or `-R` is recursive,
2. `-n` is line number, it will show the line number in the output
3. `-w` stands for match the whole word. if no such parameter, then it may find the pattern within one word. For example, we want to search 'name' in all files. If we use `-w`, then only the world `name` satisfy the requirement. Without this parameter, `hello_name` also satisfy the requierement. 
4. `-l` (lower-case L) can be added to just give the file name of matching files. Don't output the details, just give you the file name that match the pattern
5. `-L` (upper-case L) can be added to just give the file name of `without matching` files. The opposite output files name of `-l`
6. `-i` : ignore case distingctions in patterns.
7. `-e` is the pattern used during the search

```bash
grep -rnwil './' -e 'id'
```

