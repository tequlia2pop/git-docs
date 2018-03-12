# 记录更新

在你有了一个本地 Git 仓库后，你会对文件做些修改，在完成了一个阶段的目标之后，提交更新到仓库。

工作目录下的每一个文件都处于以下两种状态之一：已跟踪（tracked）和未跟踪（untracked）。

*   已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改（unmodified），已修改（modified）或已暂存（staged）。

*   工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。

初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。我们逐步将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复。

使用 Git 时文件的生命周期如下：

![](images/the-lifecycle-of-the-status-of-your-files.png)

## 检查文件状态

要查看哪些文件处于什么状态，可以用 `git status` 命令。该命令通常用来回答两个问题：（1）当前做的哪些更新还没有暂存？（2）有哪些更新已经暂存起来准备好了下次提交？

如果在克隆仓库后立即使用此命令，会看到类似这样的输出：

```
$ git status
On branch master
nothing to commit, working directory clean
```

这说明你现在的工作目录相当干净。换句话说，所有已跟踪文件在上次提交后都未被更改过。此外，上面的信息还表明，当前目录下没有出现任何处于未跟踪状态的新文件，否则 Git 会在这里列出来。 

## 状态概览

`git status` 命令的输出十分详细，但其用语有些繁琐。如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种更为紧凑的格式输出。运行 `git status -s`，状态报告输出如下：

```
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

说明如下：

*   `??` 标记：表示新添加的未跟踪文件。上面的相应输出等同于：

    ```
	Untracked files:

   		LICENSE.txt
	```

* `A` 标记：表示新添加到暂存区中的文件。上面的相应输出等同于：

	```
	Changes to be committed:

		new file:   lib/git.rb
	```

*   `M` 标记：表示修改过的文件。`M` 有两个可以出现的位置；左边的 `M ` 表示该文件被修改了并放入了暂存区；右边的 ` M` 表示该文件被修改了但是还没放入暂存区；`MM` 表示该文件被修改了并放入了暂存区，然后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录。上面的相应输出等同于：

	```
	Changes not staged for commit:

		modified:   README
		modified:   Rakefile

	Changes to be committed:

		modified:   lib/simplegit.rb
		modified:   Rakefile
	```

*   `R` 标记：表示重命名的文件。……

*   `D` 标记：表示删除的文件。……

**git status 命令和文件状态**
	
运行 `git status` 命令后，将按照以下三种类别来显示相关的文件：

*   "Changes to be committed" 表示可以提交的更新。它包含了两种类型的已暂存文件：使用 `git add` 命令添加到暂存区的新文件，以及添加到暂存区的修改文件，分别使用 "new file" 和 "modified" 标记。

*   "Changes not staged for commit"  表示尚未暂存的更新。其中的文件实际上就是尚未放入暂存区的修改文件。

*   "Untracked files" 表示未跟踪的文件。它包含了两种类型的文件：新建且尚未加入暂存区的文件，以及从暂存区删除的未修改文件。

## 跟踪新文件

要跟踪一个文件或目录，需要使用命令 `git add <file... or dir...>`，这会将这些文件放入暂存区。

例如，要跟踪 README 文件，运行：

```
$ git add README
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
```

可以在 `git add` 命令中使用通配符来跟踪多个新文件。注意，别忘了使用引号。

```
$ git add '*.txt'
```

## 暂存已修改文件

如果修改了一个已跟踪文件 CONTRIBUTING.md，然后运行 `git status` 命令，会看到下面内容：

```
$ git status
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

这说明已跟踪文件 CONTRIBUTING.md 的内容发生了变化，但还没有放到暂存区。要将一个处于已修改状态的跟踪文件放入暂存区，需要使用 `git add` 命令。

```
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   CONTRIBUTING.md
```

## 忽略文件（.gitignore 文件）

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表中。

要忽略的文件的基本原则是：

1.  操作系统自动生成的文件，比如缩略图等；

2.	编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如 Java 编译产生的 .class 文件；

3.	忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件模式。文件 `.gitignore` 的格式规范如下：

*   所有空行或者以 `＃` 开头的行都会被 Git 忽略。

*   可以使用标准的 glob 模式匹配。

*   匹配模式可以以 `/` 开头防止递归。

*   匹配模式可以以 `/` 结尾指定目录。

*   要忽略指定模式以外的文件或目录，可以在模式前加上 `!` 取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式：

*   星号（`*`）匹配零个或多个任意字符；

*   问号（`?`）只匹配一个任意字符；

*   `[abc]` 匹配任何一个列在方括号中的字符；

*   如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。

*   使用两个星号（`**`) 表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z`, `a/b/z` 或 `a/b/c/z` 等。

我们看一个 `.gitignore` 文件的例子：

```
# 忽略 .a 文件
*.a

# 跟踪 lib.a，即使你在前面已经忽略了 .a 文件
!lib.a

# 仅忽略当前目录下的 TODO 文件，不包括 subdir/TODO
/TODO

# 忽略 build/ 目录下的所有文件
build/

# 这会忽略 doc/notes.txt，而不会忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录下的所有 .pdf 文件
doc/**/*.pdf
```

TIP: GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表，你可以在 https://github.com/github/gitignore 找到它。

## 查看未暂存和已暂存的修改

要查看工作目录中的文件和暂存区快照之间的差异（也就是修改之后还没有暂存起来的变化内容），可以使用 `git diff` 命令。注意，`git diff` 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。所以有时候你一下子暂存了所有更新过的文件后，运行 `git diff` 后却什么也没有。

```
$ git diff
```

要查看暂存区快照和最新提交之间的差异（也就是已暂存的将要添加到下次提交里的变化内容），可以在 `git diff` 上增加 `--staged` 或 `--cached` 选项；也可以使用 `HEAD` 选项，它是指向当前分支中最新一次提交的指针。

```
$ git diff --staged
```

```
$ git diff --cached
```

```
$ git diff HEAD
```

建议在执行 `git commit` 命令之前先执行 `git diff HEAD` 命令，查看暂存区与上次提交之间有什么差别，等确认完毕后再进行提交。

## 提交更新

在提交之前，先使用 `git status` 检查一下所有的变化是不是都暂存起来了，然后再运行 `git commit`。

添加 `-m` 选项来记录一行提交信息：

```
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

可以看到，提交后它会告诉你，当前是在哪个分支（`master`）提交的，本次提交的完整 SHA-1 校验和是什么（`463dc4f`），以及在本次提交中，有多少文件修订过，多少行添加和删改过。

要记录详细的提交信息，直接运行 `git commit` 命令，然后编辑器就会启动。在编辑器中记录提交信息的格式如下：

*   第一行：用一行文字简述提交的更改内容；

*   第二行：空行；

*   第三行以后：记录更改的原因和详细内容。

只要按照上面的格式输入，今后便可以通过确认日志的命令或工具看到这些记录。

**跳过使用暂存区域**

Git 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤。

```
$ git commit -a -m 'added new benchmarks'
```

## 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。

可以用 `git rm` 命令从 Git 中移除文件，并连带从工作目录中删除指定的文件。如果仅仅想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中，可以使用 `--cached` 选项。

```
$ git rm --cached README
```

`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 glob 模式。注意，在星号 `*` 之前需要加一个反斜杠 `\`， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。

例如，删除以 `~` 结尾的所有文件：

```
$ git rm \*~
```

例如，删除 `log/` 目录下扩展名为 `.log` 的所有文件：

```
$ git rm log/\*.log
``` 

如果只是简单地从工作目录中手工删除文件，这些文件就会进入未暂存清单，需要再运行 `git rm` 记录这次移除文件的操作。如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 `-f`（即 `force` 的首字母）。这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

## 移动文件

不像其它的 VCS 系统，Git 并不显式跟踪文件移动操作。如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。不过 Git 非常聪明，它会推断出究竟发生了什么。

要在 Git 中对文件改名，可以这么做：

```
$ git mv file_from file_to
```

其实，运行 `git mv` 就相当于运行了下面三条命令：

```
$ mv README.md README
$ git rm README.md
$ git add README
```

## 参考：git add 命令说明

`git add` 命令可以跟踪新文件，或者把已跟踪的文件放入暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。将这个命令理解为“添加内容到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。 

要将指定的文件或目录暂存起来，可以将文件或目录的路径作为 `git add` 命令的的参数。如果参数是目录的路径，该命令将递归地处理该目录下的所有文件。

```
$ git add <file... or dir...>
```

要将所有的变化（包括新建、修改和删除）暂存起来，可以在 `git add` 命令上使用 `all` 参数。从 Git 2.0 开始，`all` 是 `git add` 命令的默认参数，所以也可以用 `git add .` 代替。

```
$ git add --all
```

```
$ git add .
```

注意，运行了 `git add` 命令之后又作了修订的文件，需要重新运行 `git add` 命令把最新版本重新暂存起来。
