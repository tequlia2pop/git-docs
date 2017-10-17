# Git 底层命令

Git 包含了一部分用于完成底层工作的命令。这些命令被设计成能以 UNIX 命令行的风格连接在一起，抑或藉由脚本调用，来完成工作。这部分命令一般被称作“底层（plumbing）”命令，而那些更友好的命令则被称作“高层（porcelain）”命令。

底层命令 | 说明 | 选项
-------- | ---- | ----
`hash-object` | 将任意数据保存于 `.git` 目录，并返回相应的键值。 | `-w` 选项指示存储数据对象；若不指定此选项，则该命令仅返回对应的键值。<br/>`--stdin` 选项则指示从标准输入读取内容；若不指定此选项，则须在命令尾部给出待存储文件的路径。
`cat-file` | 从 Git 取回数据。 | `-p` 选项可指示自动判断内容的类型并显示格式友好的内容。<br/>`-t` 选项返回指定内容的数据类型。
`update-index` | 创建暂存区 | 
`write-tree` | 将暂存区内容写入一个树对象。 |
`read-tree` | 将指定的树对象读入暂存区。 | `--prefix `
`commit-tree` | 创建一个提交对象，需要指定一个树对象的 SHA-1 值，以及该提交的父提交对象（如果有的话）。 |
`update-ref` | 更新指定的引用。 |
`symbolic-ref` | 查询或设置符号引用（HEAD 引用）。 |

## 示例说明

从标准输入读取内容，并将其存储到 Git 中，然后返回对应的键值：

```
$ echo 'test content' | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

将指定的文件存储到 Git 中，然后返回对应的键值：

```
$ git hash-object -w test.txt
83baae61804e65cc73a7201a7252750c76066a30
```

从 Git 取回指定校验和对应的数据，并自动判断内容的类型并显示格式友好的内容：

```
$ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
test content
```

从 Git 取回指定校验和对应的数据，并写入指定的文件中：

```
$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > test.txt
$ cat test.txt
version 1
```

返回指定校验和对应的数据的类型：

```
$ git cat-file -t 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
blob
```