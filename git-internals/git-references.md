# Git 引用

Git 引用（references，或缩写为 refs）实际上是一个文件，它包含了某个提交对象的 SHA-1 值。Git 将 Git 引用保存在 `.git/refs` 目录下。这样当我们需要浏览提交历史时，不需要再指定原始的提交对象的 SHA-1 值，而只需要指定 Git 引用的文件名字即可。

为了清楚目前处于哪一个分支上，Git 还提供了一个 HEAD 引用，它指向目前所在的分支。

Git 引用 | 存储内容 | 存储位置 | 是否可检出 | 是否可修改
-------- | -------- | -------- | ---------- | ----------
分支 | 提交对象的 SHA-1 值 | `.git/refs/heads/` | Y | Y
标签引用 | 提交对象的 SHA-1 值（轻量标签）<br/>标签对象的 SHA-1 值（附注标签） | `.git/refs/tags/` | N | Y
远程引用 | 提交对象的 SHA-1 值 | `.git/refs/remotes/` | Y | N
HEAD 引用 | 指向目前所在的分支 | `.git/HEAD` | Y | Y

在 Git 中你并不能检出一个标签，因为它们并不能像分支一样来回移动。 如果你想要工作目录与仓库中特定的标签版本完全一样，可以使用 `git checkout -b [branchname] [tagname]` 在特定的标签上创建一个新分支。

远程引用是只读的。虽然可以 `git checkout` 到某个远程引用，但是 Git 并不会将 HEAD 引用指向该远程引用。因此，你永远不能通过 `commit` 命令来更新远程引用。Git 将这些远程引用作为记录远程服务器上各分支最后已知位置状态的书签来管理。

## 分支

Git 分支本质上仅仅是指向提交对象的指针或引用。Git 分支对应到 `.git/refs/heads/` 下的文件。


在目前的项目中，`.git/refs` 目录没有包含任何文件，但它包含了一个简单的目录结构：

```
$ find .git/refs
.git/refs
.git/refs/heads
.git/refs/tags
$ find .git/refs -type f
```

想要在最新提交上创建一个分支，可以直接在 `.git/refs/heads/` 目录下新建一个引用文件：

```
$ echo "b6fbf7412bd9fa2bec1a5faba907f2232fa4f237" > .git/refs/heads/master
```

但是不提倡直接编辑引用文件。可以使用 `update-ref` 命令来更新某个引用：

```
$ git update-ref refs/heads/master b6fbf7412bd9fa2bec1a5faba907f2232fa4f237
```

现在要查询最新提交的历史，可以使用这个刚创建的分支来代替 SHA-1 值：

```
$ git log --pretty=oneline master
b6fbf7412bd9fa2bec1a5faba907f2232fa4f237 third commit
ba41935c2cee96a175aaf4a5ecf826390f111f2e second commit
11ea6202c0df0b25dd2af389965bbccd33a48ce1 first commit
```

在第二个提交上创建一个分支：

```
$ git update-ref refs/heads/test ba41935c2cee96a175aaf4a5ecf826390f111f2e
```

这个分支将只包含从第二个提交开始往前追溯的记录：

```
$ git log --pretty=oneline test
ba41935c2cee96a175aaf4a5ecf826390f111f2e second commit
11ea6202c0df0b25dd2af389965bbccd33a48ce1 first commit
```

现在的 Git 数据库从概念上看起来像这样：

![](images/data-model-4.png)

当运行类似于 `git branch (branchname)` 这样的命令时，Git 实际上会运行 `update-ref` 命令，取得当前所在分支最新提交对应的 SHA-1 值，并将其加入你想要创建的任何新引用中。

## HEAD 引用

HEAD 文件是一个符号引用（symbolic reference），指向目前所在的分支。 所谓符号引用，意味着它并不像普通引用那样包含一个 SHA-1 值——它是一个指向其他引用的指针。如果查看 HEAD 文件的内容，一般而言我们看到的类似这样：

```
$ cat .git/HEAD
ref: refs/heads/master
```

执行 `git checkout` 命令切换分支时，Git 会更新 HEAD 文件。例如执行 `git checkout test` 后，Git 会这样更新 HEAD 文件：

```
$ cat .git/HEAD
ref: refs/heads/test
```

很多 Git 操作依赖于当前所在的分支，这些操作都会查询 HEAD 引用来获取该信息。

*   当执行 `git branch (branchname)` 命令时，Git 会取得当前所在分支最新提交对应的 SHA-1 值，并将其加入你想要创建的任何新引用中。

*   当执行 `git commit` 命令时，Git 会创建一个提交对象，并用 HEAD 文件所指向的 SHA-1 值设置为其父提交字段。

可以手动编辑 HEAD 文件，同样可以使用一个更安全的命令来操作 HEAD 引用的值：`symbolic-ref`。

查看 HEAD 引用对应的值：

```
$ git symbolic-ref HEAD
refs/heads/master
```

设置 HEAD 引用的值：

```
$ git symbolic-ref HEAD refs/heads/test
$ cat .git/HEAD
ref: refs/heads/test
```

## 标签引用

我们知道存在两种类型的标签：轻量标签和附注标签。

轻量标签就是一个普通的 Git 引用，它包含了某个提交对象的 SHA-1 值。

创建一个轻量标签：

```
$ git update-ref refs/tags/v1.0 ba41935c2cee96a175aaf4a5ecf826390f111f2e
$ cat .git/refs/tags/v1.0
ba41935c2cee96a175aaf4a5ecf826390f111f2e
```

如果要创建一个附注标签，Git 会创建一个标签对象，并记录一个引用来指向该标签对象，而不是直接指向提交对象。 

```
$ git tag -a v1.1 b6fbf7412bd9fa2bec1a5faba907f2232fa4f237 -m 'test tag'
$ cat .git/refs/tags/v1.1
03adfc81a54a5800226f00e11c4fd9f0cefd4491
```

这里创建了一个标签对象，其 SHA-1 值为 `03adfc81a54a5800226f00e11c4fd9f0cefd4491`，可以在 `.git/objects` 目录下找到对应的文件。

我们可以查看该标签对象的内容：

```
$ git cat-file -p 03adfc81a54a5800226f00e11c4fd9f0cefd4491
object b6fbf7412bd9fa2bec1a5faba907f2232fa4f237
type commit
tag v1.1
tagger tequlia2pop <tequlia2pop@gmail.com> 1507860588 +0800

test tag
```

可以看到，标签对象非常类似于一个提交对象——它包含了一个指向 Git 对象的 SHA-1 指针、以及相应的标签创建者信息（外加一个时间戳）和注释信息。 主要的区别在于，标签对象通常指向一个提交对象，而不是一个树对象。 标签对象像是一个永不移动的分支引用——永远指向同一个提交对象，只不过给这个提交对象加上一个更友好的名字罢了。

要注意的是，标签对象并非必须指向某个提交对象；你可以对任意类型的 Git 对象打标签。

## 远程引用

远程引用是指对远程仓库的引用（指针），包括分支、标签等。

如果你添加了一个远程版本库并对其执行过推送操作，Git 会记录下最近一次推送操作时每一个分支所对应的值，并保存在 `refs/remotes` 目录下。例如，你可以添加一个叫做 `origin` 的远程版本库，然后把 `master` 分支推送上去：

```
$ git remote add origin git@github.com:tequlia2pop/git-test.git
$ git push origin master
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (9/9), 730 bytes | 0 bytes/s, done.
Total 9 (delta 0), reused 0 (delta 0)
To github.com:tequlia2pop/git-test.git
 * [new branch]      master -> master
```

现在查看 `refs/remotes/origin/master` 文件，可以发现 `origin` 远程版本库的 `master` 分支所对应的 SHA-1 值，就是最近一次与服务器通信时本地 `master` 分支所对应的 SHA-1 值：

```
$ cat .git/refs/remotes/origin/master
b6fbf7412bd9fa2bec1a5faba907f2232fa4f237
```

远程引用（位于 `refs/remotes` 目录下）和分支（位于 `refs/heads` 目录下）之间最主要的区别在于，远程引用是只读的。虽然可以 `git checkout` 到某个远程引用，但是 Git 并不会将 HEAD 引用指向该远程引用。因此，你永远不能通过 `commit` 命令来更新远程引用。Git 将这些远程引用作为记录远程服务器上各分支最后已知位置状态的书签来管理。

可以使用 `$ git ls-remote (remote)` 显式地获得远程引用的完整列表：

```
$ git ls-remote origin
b6fbf7412bd9fa2bec1a5faba907f2232fa4f237        HEAD
b6fbf7412bd9fa2bec1a5faba907f2232fa4f237        refs/heads/master
```