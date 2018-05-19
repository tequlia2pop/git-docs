# reset（重置）

## 三棵树

以 Git 的思维框架（将其作为内容管理器）来管理三棵不同的树。 “树” 在我们这里的实际意思是 “文件的集合”，而不是指特定的数据结构。

树 | 用途
-- | ----
HEAD | 上一次提交的快照，下一次提交的父节点
Index | 预期的下一次提交的快照
Working Directory | 沙盒


* HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交。这表示 HEAD 将是下一次提交的父节点。 通常，理解 HEAD 的最简方式，就是将它看做你的上一次提交的快照。

* Index 是你的预期的下一次提交。我们也会将这个概念引用为 Git 的 “暂存区域”。

* 工作目录 另外两棵树以一种高效但并不直观的方式，将它们的内容存储在 `.git` 文件夹中。 工作目录会将它们解包为实际的文件以便编辑。你可以把工作目录当做沙盒。在你将修改提交到暂存区并记录到历史之前，可以随意更改。

## 工作流程

Git 主要的目的是通过操纵这三棵树来以更加连续的状态记录项目的快照。

![](images/reset-workflow.png)

切换分支或克隆的过程也类似。当检出一个分支时，它会修改 HEAD 指向新的分支引用，将 Index 填充为该次提交的快照，然后将 Index 的内容复制到工作目录中。

## reset 的作用

reset 以一种简单可预见的方式直接操纵这三棵树。它做了三个基本操作。

1.  移动 HEAD 指向的分支

	这与改变 HEAD 自身不同（`checkout` 所做的）；reset 移动 HEAD 指向的分支。这意味着如果 HEAD 设置为 `master` 分支（例如，你正在 `master` 分支上），运行 `git reset 9e5e64a` 将会使 `master` 指向 `9e5e64a`。
	
	![](images/reset-soft.png)
	
	这本质上是撤销了上一次 `git commit` 命令。当你运行 `git commit` 时，Git 会创建一个新的提交，并移动 HEAD 所指向的分支来使其指向该提交。当你将它 `reset` 回 `HEAD~`（HEAD 的父节点）时，其实就是把该分支移动回原来的位置，而不会改变索引和工作目录。
	
2.  更新 Index（--mixed）

	接下来，`reset` 会用 HEAD 指向的当前快照的内容来更新 Index。
	
	![](images/reset-mixed.png)
	
	它依然会撤销上一次提交，但还会取消暂存所有的东西。于是，我们回滚到了所有 `git add` 和 `git commit`
	的命令执行之前。
	
3.  更新工作目录（--hard）

	reset 要做的的第三件事情就是用 Index 的内容来更新工作目录。
	
	![](images/reset-hard.png)
	
	它撤销了上一次的 `git add` 和 `git commit` 命令，而且重新覆盖了工作目录的内容。
	
**回顾**

`reset` 命令会以特定的顺序重写这三棵树，在你指定以下选项时停止：

* 移动 HEAD 分支的指向（若指定了 `--soft`，则到此停止）
* 用 HEAD 指向的当前快照的内容来更新 Index（若未指定 `--hard`，则到此停止）
* 用 Index 的内容来更新工作目录。

----

*   `git reset --soft` 命令只会移动 HEAD 指向的分支。

	例如 `git reset --soft HEAD~` 本质上是撤销了上一次 `git commit` 命令。具体来说，该命令会将分支移动回上一次提交，而不会改变 Index 和工作目录。

*   `git reset --mixed` 命令除了移动 HEAD 的指向，还会更新 Index。注意，`--mixed` 选项是 `reset` 命令的默认行为。

	例如 `git reset [--mixed] HEAD~` 本质上是撤销了上一次提交，并取消暂存的所有东西。于是，我们回滚到了 `git add` 和 `git commit` 的命令执行之前。
	
*   `git reset --hard` 命令除了移动 HEAD 的指向、更新 Index 外，还会更新工作目录。

	例如 `git rest --hard HEAD` 本质上是撤销了上一次提交，并取消暂存和工作目录的所有东西。
	
## reset & checkout

![](images/git-reset.jpg)

下面的速查表列出了 `reset` 命令和 `chekcout` 命令对树的影响。“HEAD” 一列中的 “REF” 表示该命令移动了 HEAD 指向的分支，而 “HEAD” 则表示只移动了 HEAD 自身。 特别注意 “WD Safe?” 一列 - 如果它标记为 NO，那么运行该命令之前请考虑一下。

commit 级别 | HEAD | Index | Workdir | WD Safe?
----------- | ---- | ----- | ------- | --------
`reset --soft [commit]` | REF | NO | NO | YES
`reset [commit]` | REF | YES | NO | YES
`reset --hard [commit]` | REF | YES | YES | **NO**
`checkout [commit]` | HEAD | YES | YES | YES

file 级别 | HEAD | Index | Workdir | WD Safe?
----------- | ---- | ----- | ------- | --------
`reset (commit) [file]` | NO | YES | NO | YES
`checkout (commit) [file]` | NO | YES | YES | **NO**
