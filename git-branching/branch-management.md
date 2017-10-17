# 分支管理

## 查看分支

使用 `git branch` 命令可以显示当前所有本地分支。

```
$ git branch
  iss53
* master
  testing
```

结果中具有 `*` 字符的分支就是检出的那一个分支，也就是当前 `HEAD` 指针所指向的分支。

### 查看分支的提交

可以使用 `git branch -v` 命令查看各个分支的最后一次提交。

```
$ git branch -v
  iss53   93b412c fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 add scott to the author list in the readmes
```

也可以使用 `git log` 命令查看各个分支当前指向的提交对象，提供这一功能的参数是 `--decorate`。

```
$ git log --oneline --decorate
f30ab (HEAD, master, testing) add feature #32 - ability to add new
34ac2 fixed bug #1328 - stack overflow under certain conditions
98ca9 initial commit of my project
```

可以看到，当前 “master” 和 “testing” 分支均指向校验和以 `f30ab` 开头的提交对象。

### 查看分支的分叉历史

可以 `git log --oneline --decorate --graph --all` 命令可以查看分支的分叉历史，它会输出你的提交历史、各个分支的指向以及项目的分支分叉情况：

```
$ git log --oneline --decorate --graph --all
* c2b9e (HEAD, master) made other changes
| * 87ab2 (testing) made a change
|/
* f30ab add feature #32 - ability to add new formats to the
* 34ac2 fixed bug #1328 - stack overflow under certain conditions
* 98ca9 initial commit of my project
```

### 查看分支的合并情况

如果要查看哪些分支已经合并到当前分支，可以使用 `git branch --merged` 命令：

```
$ git branch --merged
  iss53
* master
```

出现在结果中的没有带 `*` 的分支都已经合并到了当前分支上，例如 `iss53` 分支已经合并到了 `master` 分支上；通常可以删除掉这些分支。

如果要查看所有包含未合并工作的分支，可以使用 `git branch --no-merged` 命令：

```
$ git branch --no-merged
  testing
```

## 创建分支

使用 `git branch` 命令来创建分支：

```
$ git branch [branch-name]
```

这会在当前所在的提交对象上创建一个新的指针。

## 切换分支

使用 `git checkout` 命令切换到一个已存在的分支：

```
$ git checkout [branch-name]
```

这条命令做了两件事。一是使 `HEAD` 指向指定的分支，二是将工作目录恢复成指定的分支所指向的快照内容。

要切换回上一个分支，可以使用“-”（连字符）代替分支名：

```
$ git checkout -
```

## 创建并切换分支

使用 `git checkout -b` 命令可以创建一个新的分支，并切换到该分支上：

```
$ git checkout -b [branch-name]
```

以上命令实际上是下面两条命令的简写：

```
$ git branch [branch-name]
$ git checkout [branch-name]
```

## 合并分支

检出到想合并入的分支后，使用 `git merge` 命令来合并指定的分支：

```
$ git merge [branch-name]
```

这会将上述指定的分支合并到当前分支中。

## 删除分支

使用带 `-d` 选项的 `git branch` 命令来删除分支：

```
$ git branch -d [branch-name]
```

尝试使用 `git branch -d` 命令删除尚未合并的分支会失败；可以使用 `-D` 选项强制删除它。