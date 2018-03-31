---
title: git 学习笔记1
date: 2016-10-11 11:23:04
categories: git 
tags:
- git
---
> 本节讲解几个基本命令：
  * git config：配置相关信息
  * git clone：复制仓库
  * git init：初始化仓库
  * git add：添加更新内容到索引中
  * git diff：比较内容
  * git status：获取当前项目状况
  * git commit：提交
  * git branch：分支相关
  * git checkout：切换分支
  * git merge：合并分支
  * git reset：恢复版本
  * git log：查看日志
<!-- more  -->
# git 的初始化
1. git 配置
使用Git的第一件事就是设置你的名字和`email`,这些就是你在提交`commit`时的签名，每次提交记录里都会包含这些信息。使用`git config`命令进行配置：
```sh
bogon:~ yuanpinghua$ git config --global user.name "yuanph"
bogon:~ yuanpinghua$ git config --global user.email "yuanph@ushareit.com"
```
执行了上面的命令后,会在家目录(`/yuanpinghua`)下建立一个叫`.gitconfig` 的文件（该文件问隐藏文件，需要使用`ls -al`查看到. 内容一般像下面这样，可以使用vim或cat查看文件内容:

```sh
bogon:~ yuanpinghua$ cat ~/.gitconfig
[core]
	excludesfile = /Users/yuanpinghua/.gitignore_global
[difftool "sourcetree"]
	cmd = opendiff \"$LOCAL\" \"$REMOTE\"
	path =
[mergetool "sourcetree"]
	cmd = /Applications/SourceTree.app/Contents/Resources/opendiff-w.sh \"$LOCAL\" \"$REMOTE\" -ancestor \"$BASE\" -merge \"$MERGED\"
	trustExitCode = true
[user]
	name = yuanph
	email = yuanph@ushareit.com
```
这是个我电脑上的git配置信息.

> 上面的配置文件就是Git全局配置的文件，一般配置方法是`git config --global <配置名称> <配置的值>`。
如果你想使项目里的某个值与前面的全局设置有区别(例如把私人邮箱地址改为工作邮箱)，你可以在项目中使用`git config `命令不带` --global `选项来设置. 这会在你当前的项目目录下创建 `.git/config`，从而使用针对当前项目的配置。

# 获得一个Git仓库
既然我们现在把一切都设置好了，那么我们需要一个Git仓库。有两种方法可以得到它：
* 从已有的Git仓库中clone (克隆，复制).
* 新建一个仓库，把未进行版本控制的文件进行版本控制.

## Clone一个仓库
为了得一个项目的拷贝(copy),我们需要知道这个项目仓库的地址(Git URL). Git能在许多协议下使用，所以Git URL可能以`ssh://`, `http(s)://`, `git://`. 有些仓库可以通过不只一种协议来访问
我在github上创建一个名为“gittutorial”的仓库。这个仓库用来进行 clone
```sh
git clone https://github.com/zhipengbird/gittutorial.git
```
clone完成后，会在当前目录下多一个gittutorial的文件夹。这个文件夹里的内容就是我们刚才clone下来的代码。

## 初始化一个新的仓库
可以对一个已存在的文件夹用下面的命令让它置于Git的版本控制管理之下。
创建工程目录，初始化git仓库：
```sh
bogon:~ yuanpinghua$ mkdir project
bogon:~ yuanpinghua$ cd project
bogon:project yuanpinghua$ git init
Initialized empty Git repository in /Users/yuanpinghua/project/.git/
```
通过`ls -la`命令会发现`project`目录下会有一个名叫`.git` 的目录被创建，这意味着一个仓库被初始化了。可以进入到`.git`目录查看下有哪些内容


# 正常的工作流程
## 正常的工作流程
&emsp; git的基本流程如下：
  * 创建或修改文件
  * 使用git add命令添加新创建或修改的文件到本地的缓存区（Index）
  * 使用git commit命令提交到本地代码库
  * （可选，有的时候并没有可以同步的远端代码库）使用git push命令将本地代码库同步到远端代码库

进入我们刚才建立的project目录，分别创建文件file1，file2，file3：
```sh
bogon:project yuanpinghua$ touch file1 file2 file3
```
修改文件，可以使用vim编辑内容，也可以直接echo添加测试内容。
```sh
$ echo "testcontent1" >> file1
$ echo "testcontent2" >> file2
$ echo "testcontent3" >> file3
```
此时可以使用`git status`命令查看当前git仓库的状态：
```sh
$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	file1
	file2
	file3

nothing added to commit but untracked files present (use "git add" to track)

```
可以发现，有三个文件处于`untracked`状态，下一步我们就需要用`git add`命令将他们加入到缓存区（Index）。
使用git add命令将新建的文件添加到缓存区：
```sh
$ git add file1 file2 file3
```
然后再次执行`git status`就会发现新的变化：
```sh
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   file1
	new file:   file2
	new file:   file3
```
你现在为commit做好了准备，你可以使用 git diff 命令再加上 --cached 参数，看看缓存区中哪些文件被修改了。
如果没有--cached参数，git diff 会显示当前你所有已做的但没有加入到索引里的修改。
```sh
$ git diff --cached
diff --git a/file1 b/file1
new file mode 100644
index 0000000..ac65bb3
--- /dev/null
+++ b/file1
@@ -0,0 +1 @@
+testcontent1
diff --git a/file2 b/file2
new file mode 100644
index 0000000..a7db157
--- /dev/null
+++ b/file2
@@ -0,0 +1 @@
+testcontent2
diff --git a/file3 b/file3
new file mode 100644
index 0000000..be7a7f5
--- /dev/null
+++ b/file3
@@ -0,0 +1 @@
+testcontent3
```
当所有新建，修改的文件都被添加到了缓存区，我们就要使用`git commit`提交到本地仓库：
```sh
$ git commit -m 'add three files'
[master (root-commit) 2a5cf79] add three files
 3 files changed, 3 insertions(+)
 create mode 100644 file1
 create mode 100644 file2
 create mode 100644 file3
```
需要使用`-m`添加本次修改的注释，完成后就会记录一个新的项目版本。除了用`git add `命令，我们还可以用下面的命令将所有没有加到缓存区的修改也一起提交，但`-a`命令不会添加新建的文件。
```sh
$ echo "file yet add to cached" >> file1
$ git commit -a -m 'add yet cached content'
[master cabcbbf] add yet cached content
 1 file changed, 1 insertion(+)
```
```sh
$ touch file4
$ echo "edit file2" >> file2
git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   file2

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	file4

no changes added to commit (use "git add" and/or "git commit -a")
```
此时有工程下有一个新的修改和一个未跟踪的文件，我们使用g`it commit -a -m`来提交看下最后的效果：
```sh
$ git commit -a -m 'add modify'
[master 5888696] add modify
 1 file changed, 1 insertion(+)
```
使用 `git status`查询当前状态：
```sh
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	file4

nothing added to commit but untracked files present (use "git add" to track)
```
我们发现刚才新增的文件没有提交到本地的仓库里。
我们使用 `git add`,`git commit -m`将其提交到本地仓库中。
再次输入git status查看状态，会发现当前的代码库已经没有待提交的文件了，缓存区已经被清空。
>至此，我们完成了第一次代码提交，这次提交的代码中我们创建了四个新文件。需要注意的是如果是修改文件，也需要使用`git add`命令添加到缓存区才可以提交。如果是删除文件，则直接使用`git rm`命令删除后会自动将已删除文件的信息添加到缓存区，`git commit`提交后就会将本地仓库中的对应文件删除

这个时候如果本地的仓库连接到了远程Git服务器，可以使用下面的命令将本地仓库同步到远端服务器：
```sh
$ git push origin master
```
# 分支与合并
&emsp;Git的分支可以让你在主线（master分支）之外进行代码提交，同时又不会影响代码库主线。分支的作用体现在多人协作开发中，比如一个团队开发软件，你负责独立的一个功能需要一个月的时间来完成，你就可以创建一个分支，只把该功能的代码提交到这个分支，而其他同事仍然可以继续使用主线开发，你每天的提交不会对他们造成任何影响。当你完成功能后，测试通过再把你的功能分支合并到主线

## 分支
一个Git仓库可以维护很多开发分支。现在我们来创建一个新的叫 `experimental`的分支
```sh
$ git branch experimental
```
运行git branch命令可以查看当前的分支列表，已经目前的开发环境处在哪个分支上：
```sh
$ git branch
  experimental
* master
```
`experimental `分支是你刚才创建的，`master`分支是`Git`系统默认创建的主分支。星号标识了你当工作在哪个分支下，输入`git checkout `分支名可以切换到其他分支：
```sh
$ git checkout experimental
Switched to branch 'experimental'
```
切换到`experimental`分支，切换完成后，先编辑里面的一个文件，再提交(`commit`)改动，最后切换回 “`master`”分支：
```sh
$ echo 'update file1 in experimental branch' >> file1 # 修改文件
$ git status # 查看当前状态
On branch experimental
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   file1

no changes added to commit (use "git add" and/or "git commit -a")
$ git add file1 # 添加修改
$ git commit -m 'update file1' # 提交更改
[experimental f8d7347] update file1
 1 file changed, 1 insertion(+)
$ cat file1 # 查看内容
testcontent1
file yet add to cached
update file1 in experimental branch
$ git checkout master # 切换回主分支
Switched to branch 'master'
```
查看下file1中的内容会发现刚才做的修改已经看不到了。因为刚才的修改时在`experimental`分支下，现在切换回了`master`分支，目录下的文件都是`master`分支上的文件了。
现在可以在`master`分支下再作一些不同的修改:
```shell
$ echo 'update file2 in master branch' >> file2
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   file2

no changes added to commit (use "git add" and/or "git commit -a")
$ git add file2
$ git  commit -m ' update file2 on master'
[master b1f2507]  update file2 on master
 1 file changed, 1 insertion(+)
$ cat file2
testcontent2
edit file2
update file2 in master branch

```
这时，两个分支就有了各自不同的修改，分支的内容都已经不同，如何将多个分支进行合并呢？

可以通过下面的`git merge`命令来合并`experimental`到主线分支`master`:
```shell
$ git merge -m 'merge experimental branch' experimental
Merge made by the 'recursive' strategy.
 file1 | 1 +
 1 file changed, 1 insertion(+)

```
`-m`参数仍然是需要填写合并的注释信息。
由于两个branch修改了两个不同的文件，所以合并时不会有冲突，执行上面的命令后合并就完成了。

如果有冲突，比如两个分支都改了一个文件file3，则合并时会失败。首先我们在master分支上修改file3文件并提交：
```sh
# 修改文件3
$ echo 'update file3 in master branch' >> file3
$ git commit -a -m 'update file3 on master' # 提交更改
[master 042406f] update file3 on master
 1 file changed, 1 insertion(+)
```
然后切换到`experimental`，修改`file3`并提交：
```sh
$ git checkout experimental
Switched to branch 'experimental'
$ echo ' update file3 on experimental' >> file3
$ git commit -a -m 'update file3 on experimental'
[experimental b5bdaf6] update file3 on experimental
 1 file changed, 1 insertion(+)

```
切换到`master`进行合并：
```sh
$ git merge experimental
Auto-merging file3
CONFLICT (content): Merge conflict in file3
Automatic merge failed; fix conflicts and then commit the result.

```
合并失败后先用`git status`查看状态，会发现file3显示为`both modified`，查看file3内容会发现：
```sh
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   file3

no changes added to commit (use "git add" and/or "git commit -a")
$ cat file3
testcontent3
<<<<<<< HEAD
update file3 in master branch
=======
 update file3 on experimental
>>>>>>> experimental

```
上面的内容也可以使用`git diff`查看，先前已经提到`git diff`不加参数可以显示未提交到缓存区中的修改内容。
```sh
$ git diff
diff --cc file3
index c3d69fe,2d0b022..0000000
--- a/file3
+++ b/file3
@@@ -1,2 -1,2 +1,6 @@@
  testcontent3
++<<<<<<< HEAD
 +update file3 in master branch
++=======
+  update file3 on experimental
++>>>>>>> experimental

```
可以看到冲突的内容都被添加到了file3中，我们使用vim编辑这个文件，去掉git自动产生标志冲突的`<<<<<<`等符号后，根据需要只保留我们需要的内容后保存，然后使用`git add file3`和`git commit`命令来提交合并后的file3内容，这个过程是手动解决冲突的流程。
```sh
$ vi file3
$ git add file3
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

	modified:   file3

$ git commit -m 'merge file3'
[master 9cbeac6] merge file3

```
当我们完成合并后，不再需要`experimental`时，可以使用下面的命令删除：
```sh
$ git branch -d experimental
Deleted branch experimental (was b5bdaf6).

```
> `git branch -d`只能删除那些已经被当前分支的合并的分支. 如果你要强制删除某个分支的话就用`git branch –D`

## 撒销一个合并
如果你觉得你合并后的状态是一团乱麻，想把当前的修改都放弃，你可以用下面的命令回到合并之前的状态：
```sh
a$ git reset --hard HEAD^
HEAD is now at 042406f update file3 on master
$ cat file3  # 查看file3的内容，已经恢复到合并前的master上的文件内容
testcontent3
update file3 in master branch
```
## 快速向前合并
还有一种需要特殊对待的情况，在前面没有提到。通常，一个合并会产生一个合并提交(commit), 把两个父分支里的每一行内容都合并进来。

但是，如果当前的分支和另一个分支没有内容上的差异，就是说当前分支的每一个提交(commit)都已经存在另一个分支里了，git 就会执行一个“快速向前"(fast forward)操作；git 不创建任何新的提交(commit),只是将当前分支指向合并进来的分支。

# Git日志
## 查看日志
`git log`命令可以显示所有的提交(commit)：
```sh
$ git log

```
如果提交的历史纪录很长，回车会逐步显示，输入`q`可以退出。
`git log`有很多选项，可以使用`git help log`查看，例如下面的命令就是找出所有从"v2.5“开始在fs目录下的所有Makefile的修改：
```sh
$ git log v2.5.. Makefile fs/
```
Git会根据`git log`命令的参数，按时间顺序显示相关的提交(commit)。
## 日志统计
如果用--stat选项使用`git log`,它会显示在每个提交(commit)中哪些文件被修改了, 这些文件分别添加或删除了多少行内容，这个命令相当于打印详细的提交记录：
```sh
$ git log --stat
```
## 格式化日志
你可以按你的要求来格式化日志输出。`--pretty` 参数可以使用若干表现格式，如`oneline`:
```sh
$ git log --pretty=oneline
```
显示样式如下
```sh
042406fc594369852da2a708d3c1711d0e06f96e update file3 on master
26ecc96e38154b12f13e524a59258ceaf12a1ec8 merge experimental branch
b1f2507784fb22ea6e6fcbe94fe3c0f5deafc8e4  update file2 on master
f8d734757042dc5b92f6f1ea5a8cff828c339ef5 update file1
ebf80c4a15490f3356f4fd20d602de195bb9841f add file4
58886962819614df227fc19cfb9148cb286c1865 add modify
cabcbbf371f41559e6fefdd4dbc8bc057d1f19e0 add yet cached content
2a5cf798589e67b6cc7e6ba26a042e9523b5cd05 add three files
```
或者你也可以使用 short 格式:
```sh
$ git log --pretty=short
```
显示结果样式如下：
```sh
commit 042406fc594369852da2a708d3c1711d0e06f96e
Author: yuanph <yuanph@ushareit.com>

    update file3 on master

commit 26ecc96e38154b12f13e524a59258ceaf12a1ec8
Merge: b1f2507 f8d7347
Author: yuanph <yuanph@ushareit.com>

    merge experimental branch
.....
```
你也可用`medium`,`full`,`fuller`,`email` 或`raw`。 如果这些格式不完全符合你的相求， 你也可以用`--pretty=format`参数定义格式。
以下是几种格式的显示方式，有兴趣可以看下，没有兴趣的略过
* `git log --pretty=medium `
```sh
# medium
$ git log --pretty=medium
commit 042406fc594369852da2a708d3c1711d0e06f96e
Author: yuanph <yuanph@ushareit.com>
Date:   Tue Oct 11 16:14:03 2016 +0800

    update file3 on master

commit 26ecc96e38154b12f13e524a59258ceaf12a1ec8
Merge: b1f2507 f8d7347
Author: yuanph <yuanph@ushareit.com>
Date:   Tue Oct 11 16:11:45 2016 +0800

    merge experimental branch

```
* `git log --pretty=full`
```sh
$ git log --pretty=full
commit 042406fc594369852da2a708d3c1711d0e06f96e
Author: yuanph <yuanph@ushareit.com>
Commit: yuanph <yuanph@ushareit.com>

    update file3 on master

commit 26ecc96e38154b12f13e524a59258ceaf12a1ec8
Merge: b1f2507 f8d7347
Author: yuanph <yuanph@ushareit.com>
Commit: yuanph <yuanph@ushareit.com>

    merge experimental branch
```
* `git log --pretty=fuller`
```sh

$ git log --pretty=fuller
commit 042406fc594369852da2a708d3c1711d0e06f96e
Author:     yuanph <yuanph@ushareit.com>
AuthorDate: Tue Oct 11 16:14:03 2016 +0800
Commit:     yuanph <yuanph@ushareit.com>
CommitDate: Tue Oct 11 16:14:03 2016 +0800

    update file3 on master

commit 26ecc96e38154b12f13e524a59258ceaf12a1ec8
Merge: b1f2507 f8d7347
Author:     yuanph <yuanph@ushareit.com>
AuthorDate: Tue Oct 11 16:11:45 2016 +0800
Commit:     yuanph <yuanph@ushareit.com>
CommitDate: Tue Oct 11 16:11:45 2016 +0800

    merge experimental branch
```
* `git log --pretty=email`
```sh
$ git log --pretty=email
From 042406fc594369852da2a708d3c1711d0e06f96e Mon Sep 17 00:00:00 2001
From: yuanph <yuanph@ushareit.com>
Date: Tue, 11 Oct 2016 16:14:03 +0800
Subject: [PATCH] update file3 on master


From 26ecc96e38154b12f13e524a59258ceaf12a1ec8 Mon Sep 17 00:00:00 2001
From: yuanph <yuanph@ushareit.com>
Date: Tue, 11 Oct 2016 16:11:45 +0800
Subject: [PATCH] merge experimental branch

```
* `git log --pretty=raw`
```sh
$ git log --pretty=raw
commit 042406fc594369852da2a708d3c1711d0e06f96e
tree f69c2548cf938330507318e36b3e4f72b26a7e69
parent 26ecc96e38154b12f13e524a59258ceaf12a1ec8
author yuanph <yuanph@ushareit.com> 1476173643 +0800
committer yuanph <yuanph@ushareit.com> 1476173643 +0800

    update file3 on master

commit 26ecc96e38154b12f13e524a59258ceaf12a1ec8
tree 6fe01742a9fc7b236e446a18a6e0171ebe106e19
parent b1f2507784fb22ea6e6fcbe94fe3c0f5deafc8e4
parent f8d734757042dc5b92f6f1ea5a8cff828c339ef5
author yuanph <yuanph@ushareit.com> 1476173505 +0800
committer yuanph <yuanph@ushareit.com> 1476173505 +0800

    merge experimental branch
```
* `--graph` 选项可以可视化你的提交图(commit graph)，会用ASCII字符来画出一个很漂亮的提交历史(commit history)线：
```sh
$ git log --graph --pretty=oneline
* 042406fc594369852da2a708d3c1711d0e06f96e update file3 on master
*   26ecc96e38154b12f13e524a59258ceaf12a1ec8 merge experimental branch
|\  
| * f8d734757042dc5b92f6f1ea5a8cff828c339ef5 update file1
* | b1f2507784fb22ea6e6fcbe94fe3c0f5deafc8e4  update file2 on master
|/  
* ebf80c4a15490f3356f4fd20d602de195bb9841f add file4
* 58886962819614df227fc19cfb9148cb286c1865 add modify
* cabcbbf371f41559e6fefdd4dbc8bc057d1f19e0 add yet cached content
* 2a5cf798589e67b6cc7e6ba26a042e9523b5cd05 add three files
```
## 日志排序
日志记录可以按不同的顺序来显示。如果你要指定一个特定的顺序，可以为git log命令添加顺序参数。
按默认情况，提交会按逆时间顺序显示，可以指定`--topo-order`参数，让提交按拓扑顺序来显示(就是子提交在它们的父提交前显示):
```sh
$ git log --pretty=format:'%h:%s' --topo-order --graph
```
展示效果：
```sh
* 042406f:update file3 on master
*   26ecc96:merge experimental branch
|\  
| * f8d7347:update file1
* | b1f2507: update file2 on master
|/  
* ebf80c4:add file4
* 5888696:add modify
* cabcbbf:add yet cached content
* 2a5cf79:add three files
```
>你也可以用 --reverse参数来逆向显示所有提交日志。
