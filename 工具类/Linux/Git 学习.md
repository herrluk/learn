# Git 初始化

## 设置环境变量

Git的环境变量，这个设置是一次性的工作。即这些设置会在全局文件（用户主目录下的`.gitconfig`）或系统文件（`/etc/gitconfig`）中做永久的记录。

### 配置用户名和邮件地址

将在版本库提交时作为提交者的用户名和邮件地址。

```
$ git config --global user.name "Jiang Xin"
$ git config --global user.email jiangxin@ossxp.com
```

### 设置 git 别名，以便使用更为简洁的子命令

例如：输入`git ci`即相当于`git commit -s`，输入`git st`即相当于`git -p status`

- 如果拥有系统管理员权限（可以执行**sudo**命令），希望注册的命令别名能够被所有用户使用，可以执行如下命令：

  ```
  $ sudo git config --system alias.br branch
  $ sudo git config --system alias.ci "commit -s"
  $ sudo git config --system alias.co checkout
  $ sudo git config --system alias.st "-p status"
  ```

- 也可以运行下面的命令，只在本用户的全局配置中添加Git命令别名：

  ```
  $ git config --global alias.st status
  $ git config --global alias.ci "commit -s"
  $ git config --global alias.co checkout
  $ git config --global alias.br branch
  ```

## 创建新的版本库

### git init

```
$ cd /path/to/my/workspace
$ mkdir demo
$ cd demo
$ git init
初始化空的 Git 版本库于 /path/to/my/workspace/demo/.git/
```

实际上，如果Git的版本是1.6.5或更新的版本，可以在**git init**命令的后面直接输入目录名称，自动完成目录的创建。

```
$ cd /path/to/my/workspace
$ git init demo
初始化空的 Git 版本库于 /path/to/my/workspace/demo/.git/
$ cd demo
```

为了将这个新建立的文件添加到版本库，需要执行下面的命令：

```
$ git add welcome.txt
```

切记，到这里还没有完。Git和大部分其他版本控制系统都需要再执行一次提交操作，对于Git来说就是执行**git commit**命令完成提交。在提交过程中需要输入提交说明，这个要求对于Git来说是强制性的，不像其他很多版本控制系统（如CVS、Subversion）允许空白的提交说明。在Git提交时，如果在命令行不提供提交说明（没有使用`-m`参数），Git会自动打开一个编辑器，要求您在其中输入提交说明，输入完毕保存退出。

下面进行提交。为了说明方便，使用`-m`参数直接给出了提交说明。

```
$ git ci -m "initialized"
[master（根提交） 7e749cc] initialized
 1 个文件被修改，插入 1 行(+)
 create mode 100644 welcome.txt
```

从上面的命令及输出可以看出：

- 使用了Git命令别名，即**git ci**相当于执行**git commit**。在本节的一开始就进行了Git命令别名的设置。
- 通过`-m`参数设置提交说明为：”initialized”。该提交说明也显示在命令输出的第一行中。
- 命令输出的第一行还显示了当前处于名为`master`的分支上，提交ID为7e749cc，且该提交是该分支的第一个提交，即根提交（root-commit）。根提交和其他提交的区别在于没有关联的父提交，这会在后面的章节中加以讨论。
- 命令输出的第二行开始显示本次提交所做修改的统计：修改了一个文件，包含一行的插入。

## **git config**命令参数的区别？

在之前出现的**git config**命令，有的使用了`--global`参数，有的使用了`--system`参数，这两个参数有什么区别么？执行下面的命令，您就明白**git config`** 命令实际操作的文件了。

- 执行下面的命令，将打开`/path/to/my/workspace/demo/.git/config`文件进行编辑。

  ```
  $ cd /path/to/my/workspace/demo/
  $ git config -e
  ```

- 执行下面的命令，将打开`/home/jiangxin/.gitconfig`（用户主目录下的`.gitconfig`文件）全局配置文件进行编辑。

  ```
  $ git config -e --global
  ```

- 执行下面的命令，将打开`/etc/gitconfig`系统级配置文件进行编辑。

  如果Git安装在`/usr/local/bin`下，这个系统级的配置文件也可能是在`/usr/local/etc/gitconfig`。

  ```
  $ git config -e --system
  ```

Git的三个配置文件分别是版本库级别的配置文件、全局配置文件（用户主目录下）和系统级配置文件（`/etc`目录下）。其中版本库级别配置文件的优先级最高，全局配置文件其次，系统级配置文件优先级最低。这样的优先级设置就可以让版本库`.git`目录下的`config`文件中的配置可以覆盖用户主目录下的Git环境配置。而用户主目录下的配置也可以覆盖系统的Git配置文件。

执行前面的三个**git config**命令，会看到这三个级别配置文件的格式和内容，原来Git配置文件采用的是INI文件格式。示例如下：

```
$ cat /path/to/my/workspace/demo/.git/config
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
```

命令**git config**可以用于读取和更改INI配置文件的内容。使用命令**git config <section>.<key>**，来读取INI配置文件中某个配置的键值。例如读取`[core]`小节的`bare`的属性值，可以用如下命令：

```
$ git config core.bare
false
```

如果想更改或设置INI文件中某个属性的值也非常简单，命令格式是：**git config <section>.<key> <value>**。可以用如下操作：

```
$ git config a.b something
$ git config x.y.z others
```

如果打开`.git/config`文件，会看到如下内容：

```
[a]
        b = something

[x "y"]
        z = others
```

对于类似`[x "y"]`一样的配置小节，会在本书第三篇介绍远程版本库的章节中经常遇到。

从上面的介绍中，可以看到使用**git config**命令可以非常方便地操作INI文件，实际上可以用**git config**命令操作任何其他的INI文件。

- 向配置文件`test.ini`中添加配置。

  ```
  $ GIT_CONFIG=test.ini git config a.b.c.d "hello, world"
  ```

- 从配置文件`test.ini`中读取配置。

  ```
  $ GIT_CONFIG=test.ini git config a.b.c.d
  hello, world
  ```

## git暂存区

### git diff命令看到修改后的文件和版本库中文件的差异

通过不同的参数调用**git diff**命令可以看到不同版本库`welcome.txt`文件的差异。

- 不带任何选项和参数调用**git diff**显示工作区最新改动，即工作区和提交任务（提交暂存区，stage）中相比的差异。

  ```
  $ git diff
  diff --git a/welcome.txt b/welcome.txt
  index fd3c069..51dbfd2 100644
  --- a/welcome.txt
  +++ b/welcome.txt
  @@ -1,2 +1,3 @@
   Hello.
   Nice to meet you.
  +Bye-Bye.
  ```

- 将工作区和HEAD（当前工作分支）相比，会看到更多的差异。

  ```
  $ git diff HEAD
  diff --git a/welcome.txt b/welcome.txt
  index 18832d3..51dbfd2 100644
  --- a/welcome.txt
  +++ b/welcome.txt
  @@ -1 +1,3 @@
   Hello.
  +Nice to meet you.
  +Bye-Bye.
  ```

- 通过参数`--cached`或者`--staged`参数调用**git diff**命令，看到的是提交暂存区（提交任务，stage）和版本库中文件的差异。

  ```
  $ git diff --cached
  diff --git a/welcome.txt b/welcome.txt
  index 18832d3..fd3c069 100644
  --- a/welcome.txt
  +++ b/welcome.txt
  @@ -1 +1,2 @@
   Hello.
  +Nice to meet you.
  ```



```
git status -s	//-s 查看精简状态的输出
```

### 不要使用 git commit -a

实际上Git的提交命令（**git commit**）可以带上`-a`参数，对本地所有变更的文件执行提交操作，包括本地修改的文件，删除的文件，但不包括未被版本库跟踪的文件。

这个命令的确可以简化一些操作，减少用**git add**命令标识变更文件的步骤，但是如果习惯了使用这个“偷懒”的提交命令，就会丢掉Git暂存区带给用户最大的好处：对提交内容进行控制的能力。

有的用户甚至通过别名设置功能，创建指向命令**git commit -a**的别名`ci`，这更是不可取的行为，应严格禁止。在本书会很少看到使用**git commit -a**命令。

## git 对象

通过查看日志的详尽输出，会惊讶的看到非常多的“魔幻数字”，这些“魔幻数字”实际上是SHA1哈希值。

```
$ git log -1 --pretty=raw
commit e695606fc5e31b2ff9038a48a3d363f4c21a3d86
tree f58da9a820e3fd9d84ab2ca2f1b467ac265038f9
parent a0c641e92b10d8bcca1ed1bf84ca80340fdefee6
author Jiang Xin <jiangxin@ossxp.com> 1291022581 +0800
committer Jiang Xin <jiangxin@ossxp.com> 1291022581 +0800

    which version checked in?
```

一个提交中居然包含了三个SHA1哈希值表示的对象ID。

- `commit e695606fc5e31b2ff9038a48a3d363f4c21a3d86`

  这是本次提交的唯一标识。

- `tree f58da9a820e3fd9d84ab2ca2f1b467ac265038f9`

  这是本次提交所对应的目录树。

- `parent a0c641e92b10d8bcca1ed1bf84ca80340fdefee6`

  这是本地提交的父提交（上一次提交）。

研究Git对象ID的一个重量级武器就是**git cat-file**命令。用下面的命令可以查看一下这三个ID的类型。

```
$ git cat-file -t e695606
commit
$ git cat-file -t f58d
tree
$ git cat-file -t a0c6
commit
```

在引用对象ID的时候，没有必要把整个40位ID写全，只需要从头开始的几位不冲突即可。



下面再用**git cat-file**命令查看一下这几个对象的内容。

- commit对象`e695606fc5e31b2ff9038a48a3d363f4c21a3d86`

  ```
  $ git cat-file -p e695606
  tree f58da9a820e3fd9d84ab2ca2f1b467ac265038f9
  parent a0c641e92b10d8bcca1ed1bf84ca80340fdefee6
  author Jiang Xin <jiangxin@ossxp.com> 1291022581 +0800
  committer Jiang Xin <jiangxin@ossxp.com> 1291022581 +0800
  
  which version checked in?
  ```

- tree对象`f58da9a820e3fd9d84ab2ca2f1b467ac265038f9`

  ```
  $ git cat-file -p f58da9a
  100644 blob fd3c069c1de4f4bc9b15940f490aeb48852f3c42    welcome.txt
  ```

- commit对象`a0c641e92b10d8bcca1ed1bf84ca80340fdefee6`

  ```
  $ git cat-file -p a0c641e
  tree 190d840dd3d8fa319bdec6b8112b0957be7ee769
  parent 9e8a761ff9dd343a1380032884f488a2422c495a
  author Jiang Xin <jiangxin@ossxp.com> 1290999606 +0800
  committer Jiang Xin <jiangxin@ossxp.com> 1290999606 +0800
  
  who does commit?
  ```

在上面目录树（tree）对象中看到了一个新的类型的对象：blob对象。这个对象保存着文件`welcome.txt`的内容。用**git cat-file**研究一下。

- 该对象的类型为blob。

  ```
  $ git cat-file -t fd3c069c1de4f4bc9b15940f490aeb48852f3c42
  blob
  ```

- 该对象的内容就是`welcome.txt`文件的内容。

  ```
  $ git cat-file -p fd3c069c1de4f4bc9b15940f490aeb48852f3c42
  Hello.
  Nice to meet you.
  ```

这些对象保存在哪里？当然是Git库中的`objects`目录下了（ID的前两位作为目录名，后38位作为文件名）。用下面的命令可以看到这些对象在对象库中的实际位置。

```
$ for id in e695606 f58da9a a0c641e fd3c069; do \
  ls .git/objects/${id:0:2}/${id:2}*; done
.git/objects/e6/95606fc5e31b2ff9038a48a3d363f4c21a3d86
.git/objects/f5/8da9a820e3fd9d84ab2ca2f1b467ac265038f9
.git/objects/a0/c641e92b10d8bcca1ed1bf84ca80340fdefee6
.git/objects/fd/3c069c1de4f4bc9b15940f490aeb48852f3c42
```

下面的图示更加清楚的显示了Git对象库中各个对象之间的关系。

> [![../_images/git-objects.png](https://img.herrluk.icu/typoraPicture/2023-02-22-20:43:37-uZXZ6G.png)](https://gotgit.readthedocs.io/en/latest/_images/git-objects.png)

从上面的图示中很明显的看出提交（Commit）对象之间相互关联，通过相互之间的关联则很容易的识别出一条跟踪链。这条跟踪链可以在运行**git log**命令时，通过使用`--graph`参数看到。下面的命令还使用了`--pretty=raw`参数以便显示每个提交对象的parent属性。

### **git branch**是分支管理的主要命令，也可以显示当前的工作分支。

```
$ git branch
* master
```

在master分支名称前面出现一个星号表明这个分支是当前工作分支。至于为什么没有其他分支以及什么叫做分支，会在本书后面的章节揭晓。

## 2.4 Git重置

在上一章了解了版本库中对象的存储方式以及分支master的实现。即master分支在版本库的引用目录（.git/refs）中体现为一个引用文件`.git/refs/heads/master`，其内容就是分支中最新提交的提交ID。

```
$ cat .git/refs/heads/master
e695606fc5e31b2ff9038a48a3d363f4c21a3d86
```

上一章还通过对提交本身数据结构的分析，看到提交可以通过到父提交的关联实现对提交历史的追溯。注意：下面的**git log**命令中使用了`--oneline`参数，类似于`--pretty=oneline`，但是可以显示更短小的提交ID。参数`--oneline`在Git 1.6.3 及以后版本提供，老版本的Git可以使用参数`--pretty=oneline --abbrev-commit`替代。

```
$ git log --graph --oneline
* e695606 which version checked in?
* a0c641e who does commit?
* 9e8a761 initialized.
```

那么是不是有新的提交发生的时候，代表master分支的引用文件的内容会改变呢？代表master分支的引用文件的内容可以人为的改变么？本章就来探讨用**git reset**命令改变分支引用文件内容，即实现分支的重置。



引用`refs/heads/master`就好像是一个游标，在有新的提交发生的时候指向了新的提交。可是如果只可上、不可下，就不能称为“游标”。Git提供了**git reset**命令，可以将“游标”指向任意一个存在的提交ID。下面的示例就尝试人为的更改游标。（注意下面的命令中使用了`--hard`参数，会破坏工作区未提交的改动，慎用。）

### 用reflog挽救错误的重置

如果没有记下重置前master分支指向的提交ID，想要重置回原来的提交真的是一件麻烦的事情（去对象库中一个一个地找）。幸好Git提供了一个挽救机制，通过`.git/logs`目录下日志文件记录了分支的变更。默认非裸版本库（带有工作区）都提供分支日志功能，这是因为带有工作区的版本库都有如下设置：

```
$ git config core.logallrefupdates
true
```



### 总结

引用`./git/refs/heads/master`就好像是一个游标，在有新的提交发生的时候指向了新的提交。

```
$ cat .git/refs/heads/master
e695606fc5e31b2ff9038a48a3d363f4c21a3d86
```

Git提供了`git reset`命令，可以将“游标”指向任意一个存在的提交ID。

```
$ git reset --hard 81f231f
HEAD is now at 81f231f 初始化
```

查看一下master分支的日志文件`.git/logs/refs/heads/master`中的内容。下面命令显示了该文件的最后几行。为了排版的需要，还将输出中的40位的SHA1提交ID缩短。

```
$ tail -5 .git/logs/refs/heads/master
dca47ab a0c641e Jiang Xin <jiangxin@ossxp.com> 1290999606 +0800    commit (amend): who does commit?
a0c641e e695606 Jiang Xin <jiangxin@ossxp.com> 1291022581 +0800    commit: which version checked in?
e695606 4902dc3 Jiang Xin <jiangxin@ossxp.com> 1291435985 +0800    commit: does master follow this new commit?
4902dc3 e695606 Jiang Xin <jiangxin@ossxp.com> 1291436302 +0800    HEAD^: updating HEAD
e695606 9e8a761 Jiang Xin <jiangxin@ossxp.com> 1291436382 +0800    9e8a761: updating HEAD
```

可以看出这个文件记录了master分支指向的变迁，最新的改变追加到文件的末尾因此最后出现。最后一行可以看出因为执行了**git reset --hard**命令，指向的提交ID由`e695606`改变为`9e8a761`。

Git提供了一个**git reflog**命令，对这个文件进行操作。使用`show`子命令可以显示此文件的内容。

```
$ git reflog show master | head -5
9e8a761 master@{0}: 9e8a761: updating HEAD
e695606 master@{1}: HEAD^: updating HEAD
4902dc3 master@{2}: commit: does master follow this new commit?
e695606 master@{3}: commit: which version checked in?
a0c641e master@{4}: commit (amend): who does commit?
```

使用**git reflog**的输出和直接查看日志文件最大的不同在于显示顺序的不同，即最新改变放在了最前面显示，而且只显示每次改变的最终的SHA1哈希值。还有个重要的区别在于使用**git reflog**的输出中还提供一个方便易记的表达式：`<refname>@{<n>}`。这个表达式的含义是引用`<refname>`之前第<n>次改变时的SHA1哈希值。

那么将引用master切换到两次变更之前的值，可以使用下面的命令。

- 重置master为两次改变之前的值。

  ```
  $ git reset --hard master@{2}
  HEAD is now at 4902dc3 does master follow this new commit?
  ```

- 重置后工作区中文件`new-commit.txt`又回来了。

  ```
  $ ls
  new-commit.txt  welcome.txt
  ```

- 提交历史也回来了。

  ```
  $ git log --oneline
  4902dc3 does master follow this new commit?
  e695606 which version checked in?
  a0c641e who does commit?
  9e8a761 initialized.
  ```

此时如果再用**git reflog**查看，会看到恢复master的操作也记录在日志中了。

```
$ git reflog show master | head -5
4902dc3 master@{0}: master@{2}: updating HEAD
9e8a761 master@{1}: 9e8a761: updating HEAD
e695606 master@{2}: HEAD^: updating HEAD
4902dc3 master@{3}: commit: does master follow this new commit?
e695606 master@{4}: commit: which version checked in?
```

### 深入了解**git reset**命令

重置命令（**git reset**）是Git最常用的命令之一，也是最危险，最容易误用的命令。来看看**git reset**命令的用法。

```
用法一： git reset [-q] [<commit>] [--] <paths>...
用法二： git reset [--soft | --mixed | --hard | --merge | --keep] [-q] [<commit>]
```

上面列出了两个用法，其中 <commit> 都是可选项，可以使用引用或者提交ID，如果省略 <commit> 则相当于使用了HEAD的指向作为提交ID。

上面列出的两种用法的区别在于，第一种用法在命令中包含路径`<paths>`。为了避免路径和引用（或者提交ID）同名而冲突，可以在`<paths>`前用两个连续的短线（减号）作为分隔。

第一种用法（包含了路径`<paths>`的用法）**不会**重置引用，更不会改变工作区，而是用指定提交状态（<commit>）下的文件（<paths>）替换掉暂存区中的文件。例如命令**git reset HEAD <paths>**相当于取消之前执行的**git add <paths>**命令时改变的暂存区。

第二种用法（不使用路径`<paths>`的用法）则会**重置引用**。根据不同的选项，可以对暂存区或者工作区进行重置。参照下面的版本库模型图，来看一看不同的参数对第二种重置语法的影响。

> [![../_images/git-reset.png](https://img.herrluk.icu/typoraPicture/2023-02-24-16:12:34-jRKF0y.png)](https://gotgit.readthedocs.io/en/latest/_images/git-reset.png)

命令格式: git reset [–soft | –mixed | –hard ] [<commit>]

- 使用参数`--hard`，如：**git reset --hard <commit>**。

  会执行上图中的1、2、3全部的三个动作。即：

  1. 替换引用的指向。引用指向新的提交ID。
  2. 替换暂存区。替换后，暂存区的内容和引用指向的目录树一致。
  3. 替换工作区。替换后，工作区的内容变得和暂存区一致，也和HEAD所指向的目录树内容相同。

- 使用参数`--soft`，如:**git reset --soft <commit>**。

  会执行上图中的操作1。即只更改引用的指向，不改变暂存区和工作区。

- 使用参数`--mixed`或者不使用参数（缺省即为`--mixed`），如:**git reset <commit>**。

  会执行上图中的操作1和操作2。即更改引用的指向以及重置暂存区，但是不改变工作区。

下面通过一些示例，看一下重置命令的不同用法。

- 命令：**git reset**

  仅用HEAD指向的目录树重置暂存区，工作区不会受到影响，相当于将之前用**git add**命令更新到暂存区的内容撤出暂存区。引用也未改变，因为引用重置到HEAD相当于没有重置。

- 命令：**git reset HEAD**

  同上。

- 命令：**git reset -- filename**

  仅将文件`filename`撤出暂存区，暂存区中其他文件不改变。相当于对命令**git add filename**的反向操作。

- 命令：**git reset HEAD filename**

  同上。

- 命令：**git reset --soft HEAD^**

  工作区和暂存区不改变，但是引用向前回退一次。当对最新提交的提交说明或者提交的更改不满意时，撤销最新的提交以便重新提交。

  在之前曾经介绍过一个修补提交命令**git commit --amend**，用于对最新的提交进行重新提交以修补错误的提交说明或者错误的提交文件。修补提交命令实际上相当于执行了下面两条命令。（注：文件`.git/COMMIT_EDITMSG`保存了上次的提交日志）

  ```
  $ git reset --soft HEAD^
  $ git commit -e -F .git/COMMIT_EDITMSG
  ```

- 命令：**git reset HEAD^**

  工作区不改变，但是暂存区会回退到上一次提交之前，引用也会回退一次。

- 命令：**git reset --mixed HEAD^**

  同上。

- 命令：**git reset --hard HEAD^**

  彻底撤销最近的提交。引用回退到前一次，而且工作区和暂存区都会回退到上一次提交的状态。自上一次以来的提交全部丢失。