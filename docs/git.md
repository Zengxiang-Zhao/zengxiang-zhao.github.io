---
layout: default
title: git command
date:   2021-09-11 20:28:05 -0400
categories: git
nav_order: 99

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

# Delete a Git branch locally and remotely:

    * `git push -d <remote_name> <branch_name>` ,and `<remote_name>` may be origin.删除remote端brach
    * `git branch -D branch_name` : delete local branch

# git init --initial-branch=main

git 初始化时把branch的名称改为main。

```bash
git init -b trunk
```
`-b` is short for --initial-branch

# [Pretty Git branch graphs](https://stackoverflow.com/questions/1057564/pretty-git-branch-graphs)
 
throw the following two aliases in ~/.gitconfig file:
 
```bash
[alias]
lg1 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all
lg2 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
lg = !"git lg1"
```

# [Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes)

- `git remote`: It lists the shortnames of each remote handle you’ve specified
- `git remote -v`: shows you the URLs that Git has stored for the shortname to be used when reading and writing to that remote

```bash
$ git remote -v
origin  https://github.com/schacon/ticgit (fetch)
origin  https://github.com/schacon/ticgit (push)
```

- `git remote add <shortname> <url>`:增加新的remote端

```bash
$ git remote add pb https://github.com/paulboone/ticgit
```

- `$ git fetch <remote>`:从remote端下载文件到本地。下载的文件为本地不存在的文件
- `$ git push origin master`: 把本地文件上传到remote
- `$ git remote remove paul`: 删除remote paul。


# git push -f origin master

当在本地创建一个新的respotory，然后想上传到已有的github respotir 中，可以强行上传使其融合。

`-f`: force 
