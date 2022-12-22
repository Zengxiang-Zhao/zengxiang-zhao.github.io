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

# [Change the editor to vi(vim) when git commit](https://askubuntu.com/questions/1211050/default-editor-for-git-set-to-nano-how)

Sometimes when you write the command line "git commit" to write some message, the nano editor will pop out. If you don't like to use this editor, you can convert it to vi(vim) editor as shown below

```bash
git config --global core.editor "vim"

OR 

git config --global core.editor "vi"
```

# [Using git to show the difference of file in different tags or branches](https://stackoverflow.com/questions/3211809/how-to-compare-two-tags-with-git)

```bash
$ git diff tag1 tag2 

$ git diff tag1 tag2 -- some/file/name

```

# Delete a Git branch locally and remotely:

    * `git push -d <remote_name> <branch_name>` ,and `<remote_name>` may be origin.删除remote端brach
    * `git branch -D branch_name` : delete local branch

# [change the branch name from "master" to "main"](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main)

when your branch name is master, you can use the following command to change it to main.

```bash
$ git branch -m master main
```


# git checkout

```bash
git checkout id_number | tag_name

git checkout tags/<tag_name>

```

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

# git push your local repository to a new github repository

1. Create a new repository in your github
2. commit your local respository. If you haven't inited your local respository. Init your local repository: `git init`
4. [rename the branch name from 'master' to 'main'](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main) : `git branch -m master main`
5. `git remote add <remote-name> <remote-github-url>`
6. [pull the remote repository locally](https://www.educative.io/answers/the-fatal-refusing-to-merge-unrelated-histories-git-error) `git pull <remote-name> main --allow-unrelated-histories` Here `--allow-unrelated-histories` is essential. Otherwise you'll encourter a git error: “fatal: refusing to merge unrelated histories”
7. commit the changes and push to the remote github: `git push <remote-name> main`
8. The first time you use `pull` or `push` may need to do the autorization.

