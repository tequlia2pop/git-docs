# Reset（重置）

![](images/git-reset.jpg)

*   `git reset --soft` 命令只会移动 HEAD 的指向。

	例如 `git reset --soft HEAD~` 本质上是撤销了上一次 `git commit` 命令。当你运行 `git commit` 时，Git 会创建一个新的提交，并移动 HEAD 所指向的分支来使其指向该提交。具体来说，该命令会将分支移动回上一次提交，而不会改变索引和工作目录。

*   `git reset --mixed` 命令除了移动 HEAD 的指向，还会更新索引。注意，`--mixed` 选项也是 `reset` 命令的默认行为。

	例如 `git reset [--mixed] HEAD~` 本质上是撤销了上一次提交，并取消暂存的所有东西。于是，我们回滚到了所有 `git add` 和 `git commit` 的命令执行之前。
	
*   `git reset --hard` 命令除了移动 HEAD 的指向、更新索引，还会更新工作目录。

	例如 `git rest --hard HEAD` 本质上是撤销了上一次提交，并取消暂存和工作目录的所有东西。
	
	注意，`--hard` 标记是 `reset` 命令唯一的危险用法，它也是 Git 会真正地销毁数据的仅有的几个操作之一。

下面的速查表列出了 `reset` 命令和 `chekcout` 命令对树的影响。“HEAD” 一列中的 “REF” 表示该命令移动了 HEAD 指向的分支引用，而 “HEAD” 则表示只移动了 HEAD 自身。 特别注意 “WD Safe?” 一列 - 如果它标记为 NO，那么运行该命令之前请考虑一下。

\  | HEAD | Index | Workdir | WD Safe?
-- | ---- | ----- | ------- | --------
`reset --soft [commit]` | REF | NO | NO | YES
`reset [commit]` | REF | YES | NO | YES
`reset --hard [commit]` | REF | YES | YES | NO
`checkout [commit]` | HEAD | YES | YES | YES
`reset (commit) [file]` | NO | YES | NO | YES
`checkout (commit) [file]` | NO | YES | YES | NO
