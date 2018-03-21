---
title:  "git checkout 用于版本查看"
categories: [Version Control]
tags: [Github]
---

使用git进行版本管理时，不免会遇到想要退回上一步或多步操作的情况。这类操作命令包含了`git reset`, `git revert`, `git checkout`，不同情况，不同目的下，具体使用哪一个指令，配合怎样的参数设置，需要时间来熟悉。

首先回到Git最基本的运作模型上，总体上可以分为三个部分：

1.working directory: 也就是本地工作空间

2.staged snapshot: 版本快照暂存区

3.commit history: commit 历史线

![](/photos/postimages/01.svg)

>> 图片来源：https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting

不断回顾整个基本模型，有利于我们理解不同指令的区别。

在脑中回想我们在本地repo中的工作流程：1 修改了某些文件；2 通过`git add ` 存入快照暂存区；3 `git commit ` 确认加入commit。反过来我们的退回操作也可以相应地到达这三个不同的“深度”。再从时间/commit 历史上来看，我们可选择回退多少个commit点。总的来说回退操作中涉及到的两个方面就只有1深度2时间/commit跨度。

同样只用旋转一下上面那个git基本工作模型的图就可以更直观地理解回退的这两个方面：

![](/photos/postimages/commit.jpg)

X 轴代表 时间/commit 跨度

Y 轴代表回退深度

### 1 控制回退的 时间/commit 跨度

理解了控制回退的两个方面，这里先说如何控制回退跨度。不管是使用reset,revert,或是checkout都需要指明需要回退多远，因为这只有你自己知道，因此也必须由你来指定。

git 中用来控制回退多少个commit点的参数传入命令是 `HEAD~n` **n** 就是需要回退的 commit 步数。用checkout来进行演示：

##### 比如 `git checkout HEAD` 回退到当前commit点，不会有任何变化。

```ruby
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git checkout HEAD
M	file3.html.erb
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git checkout branch01
M	file3.html.erb
Already on 'branch01'
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀

```

##### `git checkout HEAD~1` 会退回到倒数第二次 commit 时的状态:

```ruby

⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git checkout HEAD~1
Note: checking out 'HEAD~1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

 git checkout -b <new-branch-name>

HEAD is now at d06928e... add some again
⮀ ~/demo/reset ⮀ ➦ d06928e ⮀
```

##### 如果不用HEAD~1 也可以直接指定 commit 点的版本号比如上面的 **d06928e** 。所以也可以写： `git checkout d06928e`

```ruby
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git checkout d06928e
Note: checking out 'd06928e'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

 git checkout -b <new-branch-name>

HEAD is now at d06928e... add some again
⮀ ~/demo/reset ⮀ ➦ d06928e ⮀ git checkout branch01
Previous HEAD position was d06928e... add some again
Switched to branch 'branch01'
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀

```

可以看到效果是一样的，可以通过 git reflog来查看历史commit 的版本信息。当用checkout切回某一次commit的状态时，working directory里的文件也会回到当时的状态，这时想要切回最新状态，只需要 `git chekcout 最新branch`就可以了，如上图所示的那样。

#### 所以有两种方式来传入回退步数：
1. 使用 `HEAD~n` 指定回退步数；
2. 直接传入commit版本号。
对于reset, revert, checkout 来说，控制回退步数都是这样的，有一点不同的是checkout 还可以用于切换分支的操作，这是最常用的操作。

### 2 git checkout 的不同参数设定控制回退范围

文章开头提到，回退深度可以分为三个层次：1 仅改变 commit 历史；2 改变 commit + staged snapshot；3 同时改变 commit + staged snapshot + 本地文件working directory。

这里还有一种增加精度的选择，指定(specify)只改变某一个文件对应的commit状态。比如我想让 file1.html.erb 回退到倒数第三个commit 的状态，但我不需要变动其他所有改过的文件，就属于这种情况。

`git checkout branch_name` 是常用的切换分支命令，他虽然会使working directory中的文件变更到指定分支的状态，但它并没有实际改变什么，它更像是一个版本查看器。

**git checkout 用于整体回退**

如果使用git checkout切回了某一个commit点，然后在这个commit 状态下进行文件修改并加入新的commit，这会出现commit分叉的情况，如果想丢掉之前commit线上"此commit点之后的变更"，可以这么操作，但不推荐，应该尽量保持 commit 历史的简单清晰。

这种情况下git checkout的作用很单纯，整体回退版本，没有太多复杂的操作。

**git checkout 用于指定文件的回退**

举例来说我现在有两个文件 file1.html.erb 和 file2.html.erb，按顺序进行了以下操作：

1 在file1中第一行加入一行代码，然后进行一次commit;<br>
2 在file1中第二行加入一行代码，然后进行一次commit;<br>
3 在file1中第三行加入一行代码，然后进行一次commit;<br>
4 在file1中第四行加入一行代码，然后进行一次commit，同时在 file2 中第一行加入一行代码，进行一次commit。

那么第4次的commit就包含了两个文件的修改。

这种情况下如果我直接 `git checkout HEAD~2` 回退两步，那么会回到file1中有前两行代码，file2中没有代码的状态。

现在我们尝试要做的是，保留file2中最后一次commit增加的代码，而让file1退回到第二次commit的状态。这就需要指定回退文件，使用： `git checkout HEAD~2 file1.html.erb`。

这时file1会回到第二次commit完成时只有前两行代码的情况，file2中的代码没有变动。同时，终端会显示目前working directory的文件状态处于已经加入 快照缓存区 。如果要保留着这个状态，可以就此进行一次新的commit，但如果想要返回查看前面第4次 commit的状态，仍然可以使用 git checkout 加commit版本号来返回。这里的操作类似使用 git checkout 的方法来让git帮你修改特定文件回到某个commit状态，然后把这个文件状态存进新的commit并记录下来，所以实际上还是一次向前的 commit 操作，你将， 仍然处于 branch01 分支。

整个过程如下：

```ruby

⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git add .
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git commit -m "first line"   #第1次 commit
[branch01 73e5923] first line
 1 file changed, 1 insertion(+)
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git add .
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git commit -m "second line"  #第2次commit
[branch01 01f6b91] second line
 1 file changed, 1 insertion(+)
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git add .
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git commit -m "third line"  #第3次commit
[branch01 5bd9375] third line
 1 file changed, 1 insertion(+)
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git add .
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git commit -m "add last line in file1 and simultaneously add some in file2"                                                                                                                                            #第4次commit
[branch01 c206ae1] add last line in file1 and simultaneously add some in file2
 2 files changed, 2 insertions(+)

 # 下面是指定对 file1 的checkout 操作
⮀ ~/demo/reset ⮀ ⭠ branch01 ⮀ git checkout HEAD~2 file1.html.erb
⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git status
On branch branch01
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   file1.html.erb

⮀ ~/demo/reset ⮀ ⭠ branch01± ⮀ git commit -m "after checkout file1 to HEAD~2, I add this commit"
[branch01 b6307d0] after checkout file1 to HEAD~2, I add this commit
 1 file changed, 2 deletions(-)

```
