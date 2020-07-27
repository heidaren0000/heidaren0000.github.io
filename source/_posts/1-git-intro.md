---
title: 1. Git 简介
category: software
date: 2020/7/27 18:45:25
---

# Git 简介

## 1. Git 有什么用？

假如你有正在修改一份文档，经过一天的修改，昨天的版本已经面目全非。这时候老板打电话来告诉你，昨天做的版本非常好，到时候直接上交昨天的版本就好。如果你能及时把昨天文档保存一份就好了

假如你在和同事一起制作一个小程序，如果团队只有2个人的话，大家直接上qq，把自己写的部分相互交换一下就好。那如果团队里面有50个人乃至100人呢？交换源码的过程就会浪费很多时间了，果然是高科技上班摸鱼。

想象一下：如果你可以保存一个文件的所有版本，乃至每一个版本的每一个文件的每一次修订记录，就可以非常方便的回滚，同时可以知道到底是哪里出了岔子，最短的时间找到问题的根源。这就太棒了，程序员福音啊，又保住我的头发了.

## 2. Git的发展历程

版本控制系统, Version Control System

### 2.1 本地版本控制系统RCS 

很多电脑依然在用RCS，比如OS X。他的原理就是根据记录所有修改作为补丁，方便计算出所有版本版本

- 本地终端管理文件
- 将文件在硬盘上的修改记录作为补丁集合记录

### 2.2 集中化版本控制系统

集中化的版本控制系统（CVCS）可以帮助开发者系统工作， 多个终端共同维护同一套代码，这种系统都有一个中央的服务器，而开发者通过与服务器进行同步，去除最新的文件或者提交更新

- 通过中央服务器进行代码托管 （中心化）
- 本地没有完整代码库，要保持在线 （需要联网）

集中化版本控制系统固然有很多优点，但是缺点也很明显：过于依赖中央服务器了，如果服务器宕机，那么整个团队就就瘫痪了，如果服务器遭受不可逆转的打击就意味着项目的修改历史就没有了，只剩下在每个成员硬盘上的当前版本快照。再者同步需要保持网络连接，如果没有联网就不能工作。

### 2.3 分布式版本管理系统

在分布式的管理系统中客户端不只是保存当前项目文件的最新快照，同时还会镜像项目所有的修改记录，这样即使服务器发生故障，每一个仓库都可以回复搜有的数据。每一次克隆（clone）操作，都是对整个代码仓库（repository）的完整备份

- 去中心化：所有终端都有完整仓库
- 无需联网

## 3. Git 是怎么来的

以下来自摘录自 Pro Git – Scott Chacon, Ben Straub （Git 官网找的教材，这是不给三教九流活路啊）

```
同生活中的许多伟大事物一样，Git 诞生于一个极富纷争大举创新的年代。 Linux 内核开源项目有着为数众多的参与者。 绝大多数的 Linux 内核维护工作都花在了提交补丁和保存归档的繁琐事务上（1991－2002年间）。 到 2002 年，整个项目组开始启用一个专有的分布式版本控制系统 BitKeeper 来管理和维护代码。
到了 2005 年，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了 Linux 内核社区免费使用 BitKeeper 的权力。 这就迫使 Linux 开源社区（特别是 Linux 的缔造者 Linus Torvalds）基于使用 BitKeeper 时的经验教训，开发出自己的版本系统。 他们对新的系统制订了若干目标： 速度 简单的设计 对非线性开发模式的强力支持（允许成千上万个并行开发的分支） 完全分布式 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量） 自诞生于 2005 年以来，Git 日臻成熟完善，在高度易用的同时，仍然保留着初期设定的目标。 它的速度飞快，极其适合管理大项目，有着令人难以置信的非线性分支管理系统（参见Git分支）
```

## 4. Git 的特性

### 4.1 直接记录快照， 而不是差异比较

传统的版本控制系统（CVS这样的）是以文件变更列表的方式存储信息，这类系统记录的是更改本身。把信息看作是一组基本文件+修改记录的集合体

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/deltas.png)

而 git 的方式类似于硬盘快照，像一个**快照流**，同时记录所有的文件改动, 有关快照, 你可以简单理解成将源文件和更改本身分开处理. 

有关快照并不是重点要了解可以看这个https://www.cnblogs.com/qcloud1001/p/9322321.html https://www.veeam.com/blog/how-snapshots-backups-work-together.html

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/snapshots.png)

git 更倾向于把数据看作是硬盘上文件系统的一组快照，对所有的文件制作快照并保存这个快照的索引。这是 git 与其他版本控制系统的重要区别，这个区别导致 git 更像 NTFS 这样的文件系统，从而有更多优点

### 4.2 几乎所有操作都在本地

无需时刻连接服务器，使得大部分操作得以在一瞬间完成。你可以进行修改，然后在有网络的时候再进行提交。

### 4.3 Git 保证完整性

git 在存储前会进行校验，如果在存储过程会产生 SHA-1 哈希值进行校验 这样如果上传过程中存在文件损坏，git 就能发现

### 4.4 Git 一般只添加数据

执行的所有 git 操作，几乎都只是在向 git 中添加数据，很难清除数据，很难有不可逆的操作。不必担心数据会丢失，非常愉快。

## 5. 三种状态

进入正式的学习之前，我们需要先打好基础：

### 5.1 Git 中文件有三种状态：

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/areas.png)

- modified：已修改，修改了文件，还没有被存到数据库中
- staged：已标记，已修改文件的当前版本做了标记，使之包含在下次提交的快照中
- committed：已提交，数据已经安全的保存在本地数据库中

### 5.2 Git 有三个工作区域

- Git 仓库 Repository (.git directory) :保存项目的元数据和对象数据库的地方，这是 Git 中最重要的部分，clone 时拷贝的就是这里的数据
- 工作目录 Working Directory : 项目的某个版本中提取出的内容，这些事 Git 仓库的压缩数据库提取出来的文件，放在磁盘上供你使用或者修改
- 工作区域（暂存区域、索引） staging Area ：基本工作流程如下 
  - 1、在工作目录中修改文件
  - 2、暂存文件，将文件快照放入暂存区域
  - 3、提交更新，找到暂存区域文件，将快照永久性存储到 Git 仓库目录



## 6. 首次运行 Git 前的配置

你可能已经安装好了 Git 了，只是在首次运行之前，还需要进行一些配置，比如你个人的身份信息，邮箱，以及你最喜欢用的编辑器

- 个人信息和邮箱
- 默认编辑器

git 自带了一个 `git config` 工具来帮助设置 git 的外观和行为配置变量. 注意这里的 global 后缀操作的是当前用户目录下的配置文件, 不是 /etc 下的系统全局文件

### 6.1 用户信息

第一件事就是要设置你自己的名字和邮箱地址, 每一次的 git 提交都会用到这些信息

```shell
git config --global user.name "Daren"
git config --global user.email heidaren0000@gmail.com
## 由于这里用了 --global , 只要执行一次, 下次默认就是这些信息, 如果想要针对特定项目使用不同的署名的话, 就把 --global 去掉吧
```

用户名和邮箱是用来对代码提交者确认身份的

如果你不设置用户信息的话, 我用的 macOS 版会自动根据你的电脑用户名和主机名生成一个, 并且在第一次提交的时候会给你看

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/Screen%20Shot%202020-07-12%20at%207.59.36%20PM.png)

### 6.2 文本编辑器

当 git 需要输入信息时,就会调用你设置的编辑器

```shell
git config --global core.editor vim
## 实际上 Windows 版 git 在安装时可以让你手动选择,我选的 sublime 真香
```

### 6.3 查看你刚刚设置的配置

```shell
git config --list
## 你可能会看到重复的文件名, 因为 git 会从不同的目录中读取配置, 比如 /etc/gitconfig 和 ~/.gitconfig , 而 git 会使用优先级最高的配置 
```

如果要查看某一项配置的话, 客户以直接在后面限定

```shell
git config user.name 
## 只查看用户名
```

## 7. Git 配置文件的存放位置

git 的 config 有三个

- /etc/gitconfig 这个是全局的 git 配置, 
- ~/.gitconfg 或者 ~/.config/git/config: 当前用户的 git 配置
- 在使用 git 的项目文件夹中的 .git/config 文件夹中的为当前项目的 git 配置

三个配置文件是相互覆盖的, 用户配置会覆盖全局配置, 项目的 git 配置会覆盖用户和全局的配置.

注意上面三个配置是在 Linux 的配置文件, Windows 和 macOS 的配置文件会有区别.

### 7.1 有关 Windows 和 Mac 的 git 配置文件

> “On Windows systems, Git looks for the .gitconfig file in the $HOME directory (C:\Users\$USER for most people). It also still looks for /etc/gitconfig, although it’s relative to the MSys root, which is wherever you decide to install Git on your Windows system when you run the installer. If you are using version 2.x or later of Git for Windows, there is also a system-level config file at C:\Documents and Settings\All Users\Application Data\Git\config on Windows XP, and in C:\ProgramData\Git\config on Windows Vista and newer. This config file can only be changed by git config -f <file> as an admin.”
>
> Excerpt From: Scott Chacon. “Pro Git.” Apple Books. 
>

至于 macOS 的配置文件, 最开始我也没有找到, 后来使用了 `git config --list --show-origin` 发现原来我安装的 git 没有用户作用域的 git 配置文件, 只有系统全局和当前项目的 git 配置文件, 不知道别的事什么情况

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/Screen%20Shot%202020-07-12%20at%207.53.02%20PM.png)

### 7.2 查看所有的 git 配置文件

要查看当前所有的 git 配置文件,  可以使用命令

```bash
git config --list --show-origin
```



## 8. Git 对象模型 The Git Object Model

之前我们了解了 Git 的文件是以按照硬盘快照的方式存储的, 类似于在硬盘分区之上建立的另一个分区.

现在我们来看看各种数据的是以什么样的方式存储在 git 中, 从而实现版本管理, 分支管理等众多特性的.

### 8.1 SHA

所有有关项目历史的信息都是用 40 位的 “对象名” 作为查找的依据(reference), 看起来像这样:

```
6ff87c4664981e4397625791c8ea3bbb5f2279a3
```

你会看到像这样的字符串在 git 里面到处都是, 作为对象的名字存在. 而每一个 SHA1 都是根据对象的内容来计算的. SHA1 是密码学的哈希计算, 这意味着几乎不可能找到两个不同的对象会计算出同一个 SHA1. 

这样做有很多的好处, 比如

- 只通过对比对象名, git 可以快速鉴别两个对象是否相同
- 由于对象名在不同仓库中计算的方法是相同的, 相同的内容在不同的仓库中对象名总是相同的.
- 只要检查文件的 SHA1 是否等于对象名, Git 就可以检查这个对象是否有错误, 是否在传输过程损坏了

### 8.2 对象

所有的 Git 对象都有三个东西: 类型, 大小, 内容, 大小基本上就是内容的大小, 内容取决于这个对象的类型是什么. 一共有四种不同的对象: “blob”, “tree”, “commit”, “tag”

- “blob” 被用来存储数据, 基本上就是一个文件
- “tree” 就像一个文件夹一样, 引用了一堆其他的 tree 和 blob 对象(也就是其他文件夹 和文件)
- “commit” 指向一个单独的树, 让它保持特定时间的样子. 另外 commit 中还包含了这个指针的元数据, 比如创建的时间, 作者, 上次提交依赖的更改记录, 指向上一个 commit 的指针等.
- “tag” 用来标记特定的 commit , 将这个 commit 作为特殊的版本 release

几乎 Git 的全部都是基于这四种不同结构类型的对象, 几乎可以看作是在你硬盘文件系统之上建立的专属于 git 的小文件系统.

### 8.3 与 SVN 的区别

要注意的地方是, 相比传统的源码管理系统 SCM 存储每一次 commit 之间的文件更改记录, git 存储的是在当前树结构中所有文件的快照

### 8.4 Blob 对象

blob 存储文件的内容

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200719143724.png)

你可以用 git show 来检验 blob, 假设 我们有了一个 blob 的 SHA 码, 我们可以这样来查看它的内容

```bash
$ git show 6ff87c4664

 Note that the only valid version of the GPL as far as this project
 is concerned is _this_ particular version of the license (ie v2, not
 v2.2 or v3.x or whatever), unless explicitly otherwise stated.
...
```

一个 blob 对象什么都不是, 只是一堆二进制数据, 不会引用其他数据, 本身也没有任何类型的属性, 甚至没有文件名

由于 blob 整个都是由自身包含的数据定义的, 如果有一个树中两个文件(或者一个仓库中不同的版本)包含相同的数据,  他们将共用同一个 blob. 对象完全是独立于目录树的, 重命名一个文件也不会影响 blob 本身以及文件关联的 blob.

### 8.5 树对象 tree 

树对象中包含了许多指向 blob 和其他树对象的指针. 树基本上展现了目录关系.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200719144309.png)

git show 也可以查看树对象, 不过 git ls-tree 可以查看更多信息. 假设我们知道了一个树对象的 SHA, 我们可以这样进行查看

```bash
$ git ls-tree fb3a8bdd0ce
100644 blob 63c918c667fa005ff12ad89437f2fdc80926e21c    .gitignore
100644 blob 5529b198e8d14decbe4ad99db3f7fb632de0439d    .mailmap
100644 blob 6ff87c4664981e4397625791c8ea3bbb5f2279a3    COPYING
040000 tree 2fb783e477100ce076f6bf57e4a6f026013dc745    Documentation
100755 blob 3c0032cec592a765692234f1cba47dfdcc3a9200    GIT-VERSION-GEN
100644 blob 289b046a443c0647624607d471289b2c7dcd470b    INSTALL
100644 blob 4eb463797adc693dc168b926b6932ff53f17d0b1    Makefile
100644 blob 548142c327a6790ff8821d67c2ee1eff7a656b52    README
...
```

如你所见, 一个树对象包含了文件条目的列表, 其中包含对象模式(mode), SHA 名, 对象类型名, 文件名, 并且按照文件名进行了排列. 树对象代表了一个目录树

被树引用的对象可能是 blob, 代表了有关文件的内容, 也可能是另一个 tree 对象, 代表了一个子目录. 由于 blob 和 tree 都是使用 SHA1 进行命名的, 如果两个树对象的内容(树中的 blob, 以及其中包含的子树的递归)如果都是相同的, 两个树的名字也是相同的, 这可以很好帮助 git 来判断两个树是否相同. 

(注意: 由于自组建的存在, 树中可能也会包含 commit 对象作为列表中的条目)

### 8.6 commit 对象

commit 对象连接了树的实体状态, 同时包含了有关 “how we got there” 和 “why we got there” 的信息

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200719150729.png)

你可以在 git show 上添加 --pretty=raw 来查看你的 commit

```bash
$ git show -s --pretty=raw 2be7fcb476
commit 2be7fcb4764f2dbcee52635b91fedb1b3dcf7ab4
tree fb3a8bdd0ceddd019615af4d57a53f43d8cee2bf
parent 257a84d9d02e90447b149af58b271c19405edb6a
author Dave Watson <dwatson@mimvista.com> 1187576872 -0400
committer Junio C Hamano <gitster@pobox.com> 1187591163 -0700

    Fix misspelling of 'suppress' in docs

    Signed-off-by: Junio C Hamano <gitster@pobox.com>
```

你可以看到, commit 中有:

- 一个树: 一个树的 SHA1 名, 代表了当前提交的时候的根树的内容
- 父对象 parent: 提交前一步的项目历史 上面的例子只有一个父对象, 合并分支的提交可能有多个父对象. 如果一个项目没有父对象, 我们就叫他 “根提交(root commit)”, 代表了项目最初的一个版本. 本个项目至少有一个根提交. 可能有多个根提交, 但不是很常见(也最好不要这么做). 归纳一下: 没有父对象的是根提交, 有一个父对象的是一般的提交, 有多个父对象的是合并一个或多个分支的提交.
- 作者: 作者的名字
- 提交者: 进行提交的人的名字, 这个可能和作者不是同一个人, 比如作者写完提交之后让提交者进行提交
- 注释: 描述这次提交

注意提交本身不包含更改的信息, 所有的更改信息都是由这次提交中的树和上次提交(父对象)的树对比计算而来的. 值得一提的是尽管 git 可以识别文件内容不变但是文件位置改变的情况, 但是它不会显式的记录文件名的更名操作. (你可以看看 git diff 的 -M 参数)

一个 commit 通常使用 git commit 进行创建, 这种情况会将 HEAD 作为父对象创建, 并且 tree 中的数据都是来自于 index 的当前存储

### 8.7 对象模型 The Object Model

现在我们已经知道了三个主要的对象类型, 我们看看他们之间是怎么相互组合的

假设我们有这样的一个小项目:

```bash
$>tree
.
|-- README
`-- lib
    |-- inc
    |   `-- tricks.rb
    `-- mylib.rb

2 directories, 3 files
```

假设我们将它提交到了 git 仓库中, 在 git 仓库中会变成这样

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200719154403.png)

你可以看到我们给每个目录都创建了树对象(包含根目录), 并且给每个文件都创建了 blob, 之后我们就可以将这个 commit 指向 root, 作为跟提交, 这样就可以知道当你的项目进行提交的时候看起来是什么样的.

根据这个模型, 你就可以理解 git 的快照了: 每次提交的时候都会重用之前没有变动的树和 blob, 正好能配上这张图. 

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/snapshots.png)

### 8.8 Tag 对象

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200719222727.png)

一个 tag 对象包含了对象名(名字就叫'对象'), 对象类型, tag 名, 创建 tag 的用户名, 以及 tag 信息, 包含了一个 tag 的 PGP 签名, 可以用 git cat file 来查看

```bash
$ git cat-file tag v1.5.0
object 437b1b20df4b356c9342dac8d38849f24ef44f27
type commit
tag v1.5.0
tagger Junio C Hamano <junkio@cox.net> 1171411200 +0000

GIT 1.5.0
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v1.4.6 (GNU/Linux)

iD8DBQBF0lGqwMbZpPMRm5oRAuRiAJ9ohBLd7s2kqjkKlq1qqC57SbnmzQCdG4ui
nLE/L9aUXdWeTFPron96DLA=
=2E+0
-----END PGP SIGNATURE-----
```

可以使用 git tag 来创建和验证 tag 对象(注意  git tag 还可以用来创建 '轻标签(lightweight tags)', 这玩意不算标签, 只是一些用"refs/tags"开头的引用而已)

### 8.9 一个例子

假设我们有一个项目的根目录包含了三个文件, 而你可以将他们全部缓存之后提交. 在添加到缓存区时会计算每一个文件的 SHA1, 然后将这版文件的 blob 存放在 git 仓库中, 并且将他们的 SHA1 名城添加进缓存区中

```bash
$ git add README test.rb LICENSE # 1. 计算每一个文件的 SHA1等信息 .2. 把文件的 blob 保存到仓库中
$ git commit -m 'Initial commit'
```

当你使用 git commit 提交的时候, git 会计算每一个子目录的 SHA1 校验, 并且将它们作为 tree 树对象存储到 git 仓库中. 之后 git 会创建一个 commit 对象, 保存元数据, 以及指向上一个 commit 的对象的指针等数据(这样后面就可以恢复到之前版本的快照了)

在我们提交完成之后, git 仓库中就包含了五个对象: 三个 blob 对象(每一个对应项目中的一个文件); 一个树对象, 其中保存了目录中文件的列表, 以及每一个文件对应的文件名和 blob; 以及一个 commit 对象, 包含元数据和指向上一个 commit 的指针

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200721143724.png)

如果你修改了其中的文件之后再次提交, 下次提交中的指针和会指向它的上一个 commit 对象

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/20200721143846.png)

## 9. git 目录和工作目录

### 9.1 git 目录

'git' 文件夹包含了你的项目的所有的 git 的历史和元数据,  包含所有对象(commits, trees, tags), 所有只想不同分支的指针等等

每一个项目都只有一个 git 目录(而传统的分支管理系统像 SVN 和 CVS 每一个子目录下都有一个跟 git 目录类似的文件夹记录文件修改), 并且这个文件夹默认存放在项目根目录中, 名字是 `.git` (默认是这样, 但不是必须这样). 如果你打开看看里面的内容, 你可以看见所有重要的文件:

```bash
$>tree -L 1
.
|-- HEAD         # 指向你当前分支的指针
|-- config       # 项目的配置文件
|-- description  # 项目的描述
|-- hooks/       # pre/post action hooks 系统默认钩子脚本目录
|-- index        # index 索引文件 (下一节会提到)
|-- logs/        # 每个分支的历史信息, refs 的历史信息
|-- objects/     # 仓库中的所有对象 (commits, trees, blobs, tags)
`-- refs/        # pointers to your branches 标识指向分支等 refs 的指针
```

(这个文件夹里面可能还会有其他的文件或者目录, 但是目前它们不重要)

### 9.2 工作目录 

git 的 '工作目录' 指的是保存当前签出(checkout)用来编辑的文件的文件夹. 当你切换分支的时候, 这个文件夹下的文件常常会被 Git 删除或者替换, 不过这很正常. 所有的文件都被保存在 git 目录中, 工作目录的作用只是提供一个简单的临时签出(checkout)区域, 你可以在这里修改文件, 之后提交给仓库

## 10 git index 索引

索引是对数据库表中一列或多列的值进行排序的一种结构, 使用索引可快速访问数据库表中的特定信息. 

git 的索引被用来作为工作目录和仓库之间的缓存区(staged area). 你可以用索引来把更改放在一起, 之后同时进行提交. 当你创建 commit(提交) 的时候, 提交的正是索引总的内容, 而不是你的工作目录中的内容

### 10.1 看看索引

最方便的方式是使用 git status 命令. 当你运行这个命令的是偶, 你可以看到哪一个文件放进了缓存区中(在 索引中), 哪一个被修改了但是没有被缓存, 以及哪一个文件根本没有被追踪(untracked).

```bash
$>git status
# On branch master
# Your branch is behind 'origin/master' by 11 commits, and can be fast-forwarded.
#
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#   modified:   daemon.c
#
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#
#   modified:   grep.c
#   modified:   grep.h
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#   blametree
#   blametree-init
#   git-gui/git-citool
```

如果你完全去掉 index, 你一般都不会都是任何数据, 只要你还有 index 中描述的的树的名字( name of the tree that it described.), 就可以把所有数据找回来

然后, 你应该对 git 内部的基本运作原理有不错的理解, 以及为什么 git 和其他的 SCM 系统有一些不同. 如果你现在不能完全理解, 也不要担心, 因为后面和我们会重新学习有关的概念. 现在我们已经准备好开始使用 git 了.

git 索引是 git 的精髓, 这里只是简单概括, 有关更加详细的内容, 可以看

https://stackoverflow.com/questions/4084921/what-does-the-git-index-contain-exactly

https://shafiul.github.io//gitbook/7_the_git_index.html

https://mirrors.edge.kernel.org/pub/software/scm/git/docs/technical/racy-git.txt

## 10. index

索引是一个包含了路径名的二进制文件, 同时还包含了文件的权限和 SHA1 信息. 用 `git ls-files` 可以查看索引的内容

```bash
$ git ls-files --stage
100644 63c918c667fa005ff12ad89437f2fdc80926e21c 0   .gitignore
100644 5529b198e8d14decbe4ad99db3f7fb632de0439d 0   .mailmap
100644 6ff87c4664981e4397625791c8ea3bbb5f2279a3 0   COPYING
100644 a37b2152bd26be2c2289e1f57a292534a51a93c7 0   Documentation/.gitignore
100644 fbefe9a45b00a54b58d94d06eca48b03d40a50e0 0   Documentation/Makefile
...
100644 2511aef8d89ab52be5ec6a5e46236b4b6bcd07ea 0   xdiff/xtypes.h
100644 2ade97b2574a9f77e7ae4002a4e07a6a38e46d07 0   xdiff/xutils.c
100644 d5de8292e05e7c36c4b68857c1cf9855e3d2f70a 0   xdiff/xutils.h
```

在旧文档里, index 被称作 “当前目录缓存” 或者 “缓存”, 它有三个属性:

1. 索引中包含了创建树 tree 对象所需的所有信息. 
   	例如: 使用 git commit 的时候会从索引中的信息创建树对象, 存储到对象数据库中, 之后将它作为新的 commit 对象的关联的树对象. 在 commit 之后, 索引中还会维持之前的数据, 用来对更改进行比对.
2. 索引实现了树对象和工作目录的快速比对
   索引会通过存储每个记录的增量(比如最后修改时间), 这个数据不会被展示出来, 也不会被存储到树对象中, 但是用这个可以实现工作目录中和索引中的文件的快速比对, 这么做让 git 不必读取全部文件就可以查看更改
3. 索引可以高效展示不同树对象之间的合并冲突, 允许每个路径名都和树的信息进行充分的关联, 让你能够快速创建三方合并
   在合并的过程中, 索引可以存储多个不同版本的单独文件, (被称为“stages”). 之前看的`git ls-files` 第三列展示的就是文件的版本数, 如果不为 0 代表存在合并冲突.

索引可以等同于缓存区, 里面充满了我们正在进行编辑的树对象.

> **HEAD**
>
> HEAD 是指向当前的分支引用的指针, 由于指向的是当前分支上一次 commit, 因此可以理解为指向的是上一次提交的文件. 这让 HEAD 可以用来把工作去恢复到上次提交的状态.