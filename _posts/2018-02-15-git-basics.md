---
title:  "GitHub Baiscs"
categories: [Version Control ℗]
tags: [Github]
---

**Core conceptions**

- Version Control 版本控制

From [git-scm.com](https://git-scm.com/):
> What is “version control”, and why should you care? Version control is a system that records changes to a file or set of files over time so that you can recall specific versions later.

版本控制系统基于时间线记录文件变化，在各个时间点生成对应的version，并且能回到特定version进行操作。


- Git

From [git-scm.com](https://git-scm.com/):
> Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.

Git 是一个免费、开源的分布式版本控制系统。


- Github

From [wikipedia](https://en.wikipedia.org/wiki/GitHub)
> GitHub (originally known as Logical Awesome LLC) is a web-based hosting service for version control using git.

Github 是一项基于Git的版本控制服务。

[github的一个官方介绍视频](https://www.youtube.com/watch?v=w3jLJU7DT5E&feature=youtu.be)，延伸了github的应用场景，不只限于编程视角。

---

**Basic behaviors**

**GitHub** (originally known as Logical Awesome LLC) is a web-based hosting service for **version control** using **git**.

---

**Discrimination of terms**

GitHub 是一个在线repo服务，它基于 Git 这个版本控制系统工作。git push/pull/remote 等指令是属于 Git 版本控制系统的，而不是 Github 所独有的。 还有其他的基于 Git 的 repo 服务, 但基本的Git指令都是通用的，比如常用的 `git push heroku` 后面的 `heroku` 并不是指推到 heroku 服务器，heroku 仍然只是一个 remote 地址的 name, 只不过这个 remote name 对应的地址不在 github 在 heroku 服务器上。remote url才是决定因素。

---
##### 常用 git 指令

- [git cheatsheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)

**git config**

- `git --version` 查看 git 版本
- `git config --global user.name "Your Name"` 设置用户名称
- `git config --global user.email "youremail@example.com"` 设置email

**Repository**

Note: 首先要确认所在目录再进行仓库的初始化操作
- `git init` 将当前目录加入 Git 追踪，这会生成一个隐藏的 `.git` 文件夹，里面包含相关的配置文件。

**Commit**

- `git status` 查看repo中的变动
- `git diff` view changes to files 查看文件相关的变动
- `git add <filename>` 将 filename 文件的变动加入 commit
- `git add .` 将所有变动加入 commit
- `git commit -m "commit message"` 写入对commit的描述信息

**Branches**

- `git branch branchname` - create a new branch named branchname. 建立新分支
- `git checkout branchname` - switch to the branch named  branchname. 切换分支
- `git checkout -b branchname` - create a new branch named branchname and switch to that branch. 建立新分支并切换到新建立的分支
- `git branch` 列出所有分支，`*`标注出当前所在分支
- `git branch -D branchname` 删除分支，如果使用 `-b` 会先多一个确认步骤
- `git branch -m new_name` 给当前分支重新命名

**Merge (locally)**
- `git merge branch_1` merge the history from branchname into the current branch. 将branch_1合并到当前分支, 注意这是本地local操作，merge 之后 branch_1 并没有被删除，改变的只是并入的分支。
- `git merge branchname:master` 将branchname分支合并到master

**Remote control**

- `git remote -v` 查看远端地址
- `git remote add <remotename> <url>` 增加一个名为remotename，地址为 url 的远端连结
- `git remote set-url <remotename> <url>` 重设一个remotename对应的url
- `git remote add <remotename> <url>` 增加一个远端地址

**push and pull**

- `git push --all` push 本地的所有进度 to your repository.
- `git push <remotename> <localbranch>` 将本地的 branchname 分支进度推到名为 remotename 的远端仓库
- `git pull <remotename> <remotebranch>` 将远端的 remotebranch 分支进度拉下并合并到本地当前分支
- `git push -u <remotename> <remotebranch>` 设置默认push的remote，下次再push的时候如果不输入 remotename 可以直接推到默认仓库

**fetch**

- `git fetch remotename` - Download objects and refs from another repository 从 remotename 地址拉取最新代码进度到本地（但不合并）
- `git fetch remotename branchname` 会拿到某一个分支的变更(不合并)
- `git fetch --dry-run` see changes to the remote before you pull in 查看远端改变但不在本地执行实际操作

**log**

- `git log` 查看最近几次commit的详细信息

```
commit 7aab70fa501268e7ba6e4b9276ab74edc0697407
Author: Caven <xll290016537@gmail.com>
Date:   Mon Mar 12 16:37:24 2018 +0800

    command line basics

commit b787171a532e7b3a8df7eddad61bdf4468ec20ce
Author: Caven <xll290016537@gmail.com>
Date:   Mon Mar 12 08:50:07 2018 +0800

    added some words

commit 78f4b1f1887dd421b12465dd109cf0225a47c3d0
Author: Caven <xll290016537@gmail.com>
Date:   Fri Feb 23 15:55:54 2018 +0800

    altered readme

commit c2b2aab7b244a8211a2ff4609ffe3b17f329eb9f
Author: Caven <xll290016537@gmail.com>
Date:   Fri Feb 23 15:43:48 2018 +0800

    chapter 15

:
```

**reset and revert**

`git revert` 和 `git reset` 都可以用来撤销更改，二者间最重要的区别在于：**git reset 会更改 commit 历史， git revert 不更改之前的commit 历史， 而是向前新增一个commit 点来记录此次revert的操作。**

在多人协作中，一定要考虑好是否要去更改commit历史。如果本地更改了 commit 历史，而其他人pull的是更改之前的版本，之后的操作会造成许多冲突的情况。

---

**附：**

[Vincent Driessen的 Git Branch Model](http://nvie.com/posts/a-successful-git-branching-model/)

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fpaggul5l7j30vy16cdmf.jpg)
