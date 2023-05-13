# git版本库

## 1. 初始化仓库

通过`git init`命令把这个目录变成Git可以管理的仓库：

```shell
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```

## 2. 把文件添加到版本库

### 第一步，gitadd

用命令`git add`告诉Git，把文件添加到仓库：

```shell
$ git add readme.txt
```

执行上面的命令，没有任何显示，这就对了，Unix的哲学是“没有消息就是好消息”，说明添加成功。

### 第二步，git commit

用命令`git commit`告诉Git，把文件提交到仓库：

```shell
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

简单解释一下`git commit`命令，`-m`后面输入的是**本次提交的说明**，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

嫌麻烦不想输入`-m "xxx"`行不行？确实有办法可以这么干，但是强烈不建议你这么干，因为输入说明对自己对别人阅读都很重要。实在不想输入说明的童鞋请自行Google，我不告诉你这个参数。

`git commit`命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。

## 说明

为什么Git添加文件需要`add`，`commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件，比如：

```shell
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

# 时光穿梭

## 版本回退

### 仓库当前状态git status

我们已经成功地添加并提交了一个readme.txt文件，现在，是时候继续工作了，于是，我们继续修改readme.txt文件，改成如下内容：

```shell
Git is a distributed version control system.
Git is free software.
```

现在，运行`git status`命令看看结果：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.txt`被修改过了，但还没有准备提交的修改。

### 对比差别git diff

虽然Git告诉我们`readme.txt`被修改了，但如果能看看具体修改了什么内容，自然是很好的。比如你休假两周从国外回来，第一天上班时，已经记不清上次怎么修改的`readme.txt`，所以，需要用`git diff`这个命令看看：

```shell
$ git diff readme.txt 
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```

`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个`distributed`单词。

### 干净的工作目录

提交后，我们再用`git status`命令看看仓库的当前状态：

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

Git告诉我们当前没有需要提交的修改，而且，工作目录是干净（working tree clean）的。

### git log——查看提交历史

当然了，在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用`git log`命令查看：

```shell
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

`git log`命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是`append GPL`，上一次是`add distributed`，最早的一次是`wrote a readme file`。

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

```shell
$ git log --pretty=oneline
1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
e475afc93c209a690c39c13a46716e8fa000c366 add distributed
eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file
```

需要友情提示的是，你看到的一大串类似`1094adb...`的是`commit id`（版本号），和SVN不一样

### 回退上一版本

好了，现在我们启动时光穿梭机，准备把`readme.txt`回退到上一个版本，也就是`add distributed`的那个版本，怎么做呢？

首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```shell
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

`--hard`参数有啥意义？这个后面再讲，现在你先放心使用。

看看`readme.txt`的内容是不是版本`add distributed`：

```shell
$ cat readme.txt
Git is a distributed version control system.
Git is free software.
```

果然被还原了。

### 回溯特定id版本

还可以继续回退到上一个版本`wrote a readme file`，不过且慢，让我们用`git log`再看看现在版本库的状态：

```shell
$ git log
commit e475afc93c209a690c39c13a46716e8fa000c366 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

最新的那个版本`append GPL`已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？

办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个`append GPL`的`commit id`是`1094adb...`，于是就可以指定回到未来的某个版本：

```shell
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

再小心翼翼地看看`readme.txt`的内容：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

果然，我胡汉三又回来了。

### 解释HEAD指针

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`append GPL`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   └──▶ ○ append GPL
        │
        ○ add distributed
        │
        ○ wrote a readme file
```

改为指向`add distributed`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   │    ○ append GPL
   │    │
   └──▶ ○ add distributed
        │
        ○ wrote a readme file
```

然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

### 查找commit id——git reflog(查看命令历史)

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

在Git中，总是有后悔药可以吃的。当你用`$ git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的commit id。Git提供了一个命令`git reflog`用来记录你的每一次命令：

```shell
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

终于舒了口气，从输出可知，`append GPL`的commit id是`1094adb`，现在，你又可以乘坐时光机回到未来了。

## 解释原理

### 工作区与暂存区存

`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

https://www.liaoxuefeng.com/wiki/896043488029600/897271968352576

### 管理修改

https://www.liaoxuefeng.com/wiki/896043488029600/897884457270432

### 撤销修改

你可以发现，Git会告诉你，`git checkout -- file`可以丢弃工作区的修改：

```shell
$ git checkout -- readme.txt
```

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

现在，看看`readme.txt`的文件内容：

```shell
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
```

文件内容果然复原了。

`git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

### 删除文件

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：

```shell
$ rm test.txt
```

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

```shell
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

现在，文件就从版本库中被删除了。

# 远程仓库

## github的ssh配置

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```shell
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容：

![github-addkey-1](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157634.png)

点“Add Key”，你就应该看到已经添加的Key：

![github-addkey-2](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157988.png)

最后友情提示，在GitHub上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。

如果你不想让别人看到Git库，有两个办法，一个是交点保护费，**让GitHub把公开的仓库变成私有的**，这样别人就看不见了（不可读更不可写）。另一个办法是自己动手，搭一个Git服务器，因为是你自己的Git服务器，所以别人也是看不见的。这个方法我们后面会讲到的，相当简单，公司内部开发必备。

## 添加远程库——create a new repo


现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，**GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作**，真是一举多得。

首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：

![github-create-repo-1](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157359.png)

在Repository name填入`learngit`，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库：

![github-create-repo-2](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157426.png)

目前，在GitHub上的这个`learngit`仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

## 推送远程库git push

现在，我们根据GitHub的提示，在本地的`learngit`仓库下运行命令：

```shell
$ git remote add origin git@github.com:michaelliao/learngit.git
$ git remote add origin https://github.com/perfectddt/gitlearn.git
```

请千万注意，把上面的`michaelliao`替换成你自己的GitHub账户名，否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的SSH Key公钥不在我的账户列表中。

添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

```shell
$ git push -u origin master
Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learngit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

### github示例

Get started by [creating a new file](https://github.com/perfectddt/gitlearn/new/main) or [uploading an existing file](https://github.com/perfectddt/gitlearn/upload). We recommend every repository include a [README](https://github.com/perfectddt/gitlearn/new/main?readme=1), [LICENSE](https://github.com/perfectddt/gitlearn/new/main?filename=LICENSE.md), and [.gitignore](https://github.com/perfectddt/gitlearn/new/main?filename=.gitignore).

#### …or create a new repository on the command line

```shell
echo "# gitlearn" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/perfectddt/gitlearn.git
git push -u origin main
```

#### …or push an existing repository from the command line

```shell
git remote add origin https://github.com/perfectddt/gitlearn.git
git branch -M main
git push -u origin main
```

#### …or import code from another repository

You can initialize this repository with code from a Subversion, Mercurial, or TFS project.

#### or import code from another repository
```shell
Import code
```

## 从远程库克隆git clone

现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

首先，登陆GitHub，创建一个新的仓库，名字叫`gitskills`：

![github-init-repo](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157340.png)

我们勾选`Initialize this repository with a README`，这样GitHub会自动为我们创建一个`README.md`文件。创建完毕后，可以看到`README.md`文件：

![github-init-repo-2](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157040.png)

现在，远程库已经准备好了，下一步是用命令`git clone`克隆一个本地库：

```shell
$ git clone git@github.com:michaelliao/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```

注意把Git库的地址换成你自己的，然后进入`gitskills`目录看看，已经有`README.md`文件了：

```shell
$ cd gitskills
$ ls
README.md
```

# 分支管理

如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，两个平行宇宙合并了，结果，你既学会了Git又学会了SVN！

![learn-branches](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157309.png)

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

## 分支的原理

在[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)里，你已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即`master`分支。`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

一开始的时候，`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点：

```ascii
                  HEAD
                    │
                    │
                    ▼
                 master
                    │
                    │
                    ▼
┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘
```

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：

```ascii
                 master
                    │
                    │
                    ▼
┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘
                    ▲
                    │
                    │
                   dev
                    ▲
                    │
                    │
                  HEAD
```

你看，Git创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变：

```ascii
                 master
                    │
                    │
                    ▼
┌───┐    ┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘    └───┘
                             ▲
                             │
                             │
                            dev
                             ▲
                             │
                             │
                           HEAD
```

假如我们在`dev`上的工作完成了，就可以把`dev`合并到`master`上。Git怎么合并呢？最简单的方法，就是直接把`master`指向`dev`的当前提交，就完成了合并：

```ascii
                           HEAD
                             │
                             │
                             ▼
                          master
                             │
                             │
                             ▼
┌───┐    ┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘    └───┘
                             ▲
                             │
                             │
                            dev
```

所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

```ascii
                           HEAD
                             │
                             │
                             ▼
                          master
                             │
                             │
                             ▼
┌───┐    ┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘    └───┘
```

真是太神奇了，你看得出来有些提交是通过分支完成的吗？

## 创建并切换分支

下面开始实战。

首先，我们创建`dev`分支，然后切换到`dev`分支：

```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```shell
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

然后，用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  master
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

然后，我们就可以在`dev`分支上正常提交，比如对`readme.txt`做个修改，加上一行：

```shell
Creating a new branch is quick.
```

然后提交：

```shell
$ git add readme.txt 
$ git commit -m "branch test"
[dev b17d20e] branch test
 1 file changed, 1 insertion(+)
```

现在，`dev`分支的工作完成，我们就可以切换回`master`分支：

```shell
$ git checkout master
Switched to branch 'master'
```

切换回`master`分支后，再查看一个`readme.txt`文件，刚才添加的内容不见了！因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变：

![git-br-on-master](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157338.png)

## 合并分支——快进合并

现在，我们把`dev`分支的工作成果合并到`master`分支上：

```shell
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

`git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能`Fast-forward`，我们后面会讲其他方式的合并。

合并完成后，就可以放心地删除`dev`分支了：

```shell
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

删除后，查看`branch`，就只剩下`master`分支了：

```shell
$ git branch
* master
```

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

## 解决冲突

### 准备新分支

准备新的`feature1`分支，继续我们的新分支开发：

```shell
$ git switch -c feature1
Switched to a new branch 'feature1'
```

修改`readme.txt`最后一行，改为：

```shell
Creating a new branch is quick AND simple.
```

在`feature1`分支上提交：

```shell
$ git add readme.txt

$ git commit -m "AND simple"
[feature1 14096d0] AND simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

切换到`master`分支：

```shell
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
```

Git还会自动提示我们当前`master`分支比远程的`master`分支要超前1个提交。

在`master`分支上把`readme.txt`文件的最后一行改为：

```shell
Creating a new branch is quick & simple.
```

提交：

```shell
$ git add readme.txt 
$ git commit -m "& simple"
[master 5dc6824] & simple
 1 file changed, 1 insertion(+), 1 deletion(-)
```

现在，`master`分支和`feature1`分支各自都分别有新的提交，变成了这样：

```ascii
                            HEAD
                              │
                              │
                              ▼
                           master
                              │
                              │
                              ▼
                            ┌───┐
                         ┌─▶│   │
┌───┐    ┌───┐    ┌───┐  │  └───┘
│   │───▶│   │───▶│   │──┤
└───┘    └───┘    └───┘  │  ┌───┐
                         └─▶│   │
                            └───┘
                              ▲
                              │
                              │
                          feature1
```

### 发现冲突

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，我们试试看：

```shell
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

果然冲突了！Git告诉我们，`readme.txt`文件存在冲突，必须手动解决冲突后再提交。`git status`也可以告诉我们冲突的文件：

```shell
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

### 查看冲突文件

我们可以直接查看readme.txt的内容：

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，我们修改如下后保存：

```
Creating a new branch is quick and simple.
```

再提交：

```shell
$ git add readme.txt 
$ git commit -m "conflict fixed"
[master cf810e4] conflict fixed
```

现在，`master`分支和`feature1`分支变成了下图所示：

```ascii
                                     HEAD
                                       │
                                       │
                                       ▼
                                    master
                                       │
                                       │
                                       ▼
                            ┌───┐    ┌───┐
                         ┌─▶│   │───▶│   │
┌───┐    ┌───┐    ┌───┐  │  └───┘    └───┘
│   │───▶│   │───▶│   │──┤             ▲
└───┘    └───┘    └───┘  │  ┌───┐      │
                         └─▶│   │──────┘
                            └───┘
                              ▲
                              │
                              │
                          feature1
```

### 查看分支合并情况

用带参数的`git log`也可以看到分支的合并情况：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   cf810e4 (HEAD -> master) conflict fixed
|\  
| * 14096d0 (feature1) AND simple
* | 5dc6824 & simple
|/  
* b17d20e branch test
* d46f35e (origin/master) remove test.txt
* b84166e add test.txt
* 519219b git tracks changes
* e43a48b understand how stage works
* 1094adb append GPL
* e475afc add distributed
* eaadf4e wrote a readme file
```

### 删除分支

最后，删除`feature1`分支：

```shell
$ git branch -d feature1
Deleted branch feature1 (was 14096d0).
```

工作完成。

## 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

下面我们实战一下`--no-ff`方式的`git merge`：

首先，仍然创建并切换`dev`分支：

```shell
$ git switch -c dev
Switched to a new branch 'dev'
```

修改readme.txt文件，并提交一个新的commit：

```shell
$ git add readme.txt 
$ git commit -m "add merge"
[dev f52c633] add merge
 1 file changed, 1 insertion(+)
```

现在，我们切换回`master`：

```shell
$ git switch master
Switched to branch 'master'
```

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

```shell
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

合并后，我们用`git log`看看分支历史：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   e1e9c68 (HEAD -> master) merge with no-ff
|\  
| * f52c633 (dev) add merge
|/  
*   cf810e4 conflict fixed
...
```

可以看到，不使用`Fast forward`模式，merge后就像这样：

![git-no-ff-mode](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157086.png)



### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![git-br-policy](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157780.png)

### 小结

Git分支十分强大，在团队开发中应该充分应用。

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

## Bug分支

### 修复master分支上的bug

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支`issue-101`来修复它，但是，等等，当前正在`dev`上进行的工作还没有提交：

```shell
$ git status
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt
```

### git stash储藏当前工作现场

并不是你不想提交，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复该bug，怎么办？

幸好，Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```shell
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

现在，用`git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```shell
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

现在修复bug，需要把“Git is free software ...”改为“Git is a free software ...”，然后提交：

```shell
$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支：

```shell
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到`dev`分支干活了！

```shell
$ git switch dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```

### git stash list查看之前工作现场

工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：

```shell
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

```shell
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```

再用`git stash list`查看，就看不到任何stash内容了：

```
$ git stash list
```

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```shell
$ git stash apply stasshell
```

### dev分支上bug同时修复

在master分支上修复了bug后，我们要想一想，dev分支是早期从master分支分出来的，所以，这个bug其实在当前dev分支上也存在。

那怎么在dev分支上修复同样的bug？重复操作一次，提交不就行了？

有木有更简单的方法？

有！

### cherry-pick复制特定的提交

同样的bug，要在dev上修复，我们只需要把`4c805e2 fix bug 101`这个提交所做的修改“复制”到dev分支。注意：我们只想复制`4c805e2 fix bug 101`这个提交所做的修改，并不是把整个master分支merge过来。

为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：

```shell
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git自动给dev分支做了一次提交，注意这次提交的commit是`1d4b803`，它并不同于master的`4c805e2`，因为这两个commit只是改动相同，但确实是两个不同的commit。用`git cherry-pick`，我们就不需要在dev分支上手动再把修bug的过程重复一遍。

有些聪明的童鞋会想了，既然可以在master分支上修复bug后，在dev分支上可以“重放”这个修复过程，那么直接在dev分支上修复bug，然后在master分支上“重放”行不行？当然可以，不过你仍然需要`git stash`命令保存现场，才能从dev分支切换到master分支。

### 小结

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

## Feature分支——开发新功能

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

现在，你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。

于是准备开发：

```shell
$ git switch -c feature-vulcan
Switched to a new branch 'feature-vulcan'
```

5分钟后，开发完毕：

```shell
$ git add vulcan.py

$ git status
On branch feature-vulcan
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   vulcan.py

$ git commit -m "add feature vulcan"
[feature-vulcan 287773e] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
```

切回`dev`，准备合并：

```shell
$ git switch dev
```

一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

但是！

就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

```shell
$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

销毁失败。Git友情提醒，`feature-vulcan`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。。

现在我们强行删除：

```shell
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
```

终于删除成功！

### 小结

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

## 多人协作

### 查看远程库的信息

当你从远程仓库克隆时，实际上Git自动把本地的`master`分支和远程的`master`分支对应起来了，并且，远程仓库的默认名称是`origin`。

要查看远程库的信息，用`git remote`：

```shell
$ git remote
origin
```

或者，用`git remote -v`显示更详细的信息：

```shell
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```shell
$ git push origin master
```

如果要推送其他分支，比如`dev`，就改成：

```shell
$ git push origin dev
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！

### 抓取分支

多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：

```shell
$ git clone git@github.com:michaelliao/learngit.git
Cloning into 'learngit'...
remote: Counting objects: 40, done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 40 (delta 14), reused 40 (delta 14), pack-reused 0
Receiving objects: 100% (40/40), done.
Resolving deltas: 100% (14/14), done.
```

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的`master`分支。不信可以用`git branch`命令看看：

```shell
$ git branch
* master
```

现在，你的小伙伴要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

```shell
$ git checkout -b dev origin/dev
```

现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程：

```shell
$ git add env.txt

$ git commit -m "add env"
[dev 7a5e5dd] add env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   f52c633..7a5e5dd  dev -> dev
```

### 协作冲突

你的小伙伴已经向`origin/dev`分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

```shell
$ cat env.txt
env

$ git add env.txt

$ git commit -m "add new env"
[dev 7bd91f1] add new env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
To github.com:michaelliao/learngit.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送：

```shell
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

`git pull`也失败了，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

```shell
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

再pull：

```shell
$ git pull
Auto-merging env.txt
CONFLICT (add/add): Merge conflict in env.txt
Automatic merge failed; fix conflicts and then commit the result.
```

这回`git pull`成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的[解决冲突](http://www.liaoxuefeng.com/wiki/896043488029600/900004111093344)完全一样。解决后，提交，再push：

```shell
$ git commit -m "fix env conflict"
[dev 57c53ab] fix env conflict

$ git push origin dev
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 621 bytes | 621.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   7a5e5dd..57c53ab  dev -> dev
```



因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

### 小结

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

## Rebase——分叉提交历史整理成直线

在上一节我们看到了，多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后push的童鞋不得不先pull，在本地合并，然后才能push成功。

每次合并再push后，分支变成了这样：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
* d1be385 (HEAD -> master, origin/master) init hello
*   e5e69f1 Merge branch 'dev'
|\  
| *   57c53ab (origin/dev, dev) fix env conflict
| |\  
| | * 7a5e5dd add env
| * | 7bd91f1 add new env
| |/  
* |   12a631b merged bug fix 101
|\ \  
| * | 4c805e2 fix bug 101
|/ /  
* |   e1e9c68 merge with no-ff
|\ \  
| |/  
| * f52c633 add merge
|/  
*   cf810e4 conflict fixed
```

总之看上去很乱，有强迫症的童鞋会问：为什么Git的提交历史不能是一条干净的直线？

其实是可以做到的！

Git有一种称为rebase的操作，有人把它翻译成“变基”。

![rebase](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272157003.jpeg)

先不要随意展开想象。我们还是从实际问题出发，看看怎么把分叉的提交变成直线。

在和远程分支同步后，我们对`hello.py`这个文件做了两次提交。用`git log`命令看看：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
* 582d922 (HEAD -> master) add author
* 8875536 add comment
* d1be385 (origin/master) init hello
*   e5e69f1 Merge branch 'dev'
|\  
| *   57c53ab (origin/dev, dev) fix env conflict
| |\  
| | * 7a5e5dd add env
| * | 7bd91f1 add new env
...
```

注意到Git用`(HEAD -> master)`和`(origin/master)`标识出当前分支的HEAD和远程origin的位置分别是`582d922 add author`和`d1be385 init hello`，本地分支比远程分支快两个提交。

现在我们尝试推送本地分支：

```shell
$ git push origin master
To github.com:michaelliao/learngit.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

很不幸，失败了，这说明有人先于我们推送了远程分支。按照经验，先pull一下：

```shell
$ git pull
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:michaelliao/learngit
   d1be385..f005ed4  master     -> origin/master
 * [new tag]         v1.0       -> v1.0
Auto-merging hello.py
Merge made by the 'recursive' strategy.
 hello.py | 1 +
 1 file changed, 1 insertion(+)
```

再用`git status`看看状态：

```shell
$ git status
On branch master
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

加上刚才合并的提交，现在我们本地分支比远程分支超前3个提交。

用`git log`看看：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
*   e0ea545 (HEAD -> master) Merge branch 'master' of github.com:michaelliao/learngit
|\  
| * f005ed4 (origin/master) set exit=1
* | 582d922 add author
* | 8875536 add comment
|/  
* d1be385 init hello
...
```

对强迫症童鞋来说，现在事情有点不对头，提交历史分叉了。如果现在把本地分支push到远程，有没有问题？

有！

什么问题？

不好看！

有没有解决方法？

有！

这个时候，rebase就派上了用场。我们输入命令`git rebase`试试：

```shell
$ git rebase
First, rewinding head to replay your work on top of it...
Applying: add comment
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
Applying: add author
Using index info to reconstruct a base tree...
M	hello.py
Falling back to patching base and 3-way merge...
Auto-merging hello.py
```

输出了一大堆操作，到底是啥效果？再用`git log`看看：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master) add author
* 3611cfe add comment
* f005ed4 (origin/master) set exit=1
* d1be385 init hello
...
```

原本分叉的提交现在变成一条直线了！这种神奇的操作是怎么实现的？其实原理非常简单。我们注意观察，发现Git把我们本地的提交“挪动”了位置，放到了`f005ed4 (origin/master) set exit=1`之后，这样，整个提交历史就成了一条直线。rebase操作前后，最终的提交内容是一致的，但是，我们本地的commit修改内容已经变化了，它们的修改不再基于`d1be385 init hello`，而是基于`f005ed4 (origin/master) set exit=1`，但最后的提交`7e61ed4`内容是一致的。

这就是rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。

最后，通过push操作把本地分支推送到远程：

```shell
Mac:~/learngit michael$ git push origin master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 576 bytes | 576.00 KiB/s, done.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To github.com:michaelliao/learngit.git
   f005ed4..7e61ed4  master -> master
```

再用`git log`看看效果：

```shell
$ git log --graph --pretty=oneline --abbrev-commit
* 7e61ed4 (HEAD -> master, origin/master) add author
* 3611cfe add comment
* f005ed4 set exit=1
* d1be385 init hello
...
```

远程分支的提交历史也是一条直线。

### 小结

- rebase操作可以把本地未push的分叉提交历史整理成直线；
- git pull --rebase origin master
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

# 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

所以，tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

## 创建标签

在Git中打标签非常简单，首先，切换到需要打标签的分支上：

```shell
$ git branch
* dev
  master
$ git checkout master
Switched to branch 'master'
```

然后，敲命令`git tag <name>`就可以打一个新标签：

```shell
$ git tag v1.0
```

可以用命令`git tag`查看所有标签：

```shell
$ git tag
v1.0
```

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

方法是找到历史提交的commit id，然后打上就可以了：

```shell
$ git log --pretty=oneline --abbrev-commit
12a631b (HEAD -> master, tag: v1.0, origin/master) merged bug fix 101
4c805e2 fix bug 101
e1e9c68 merge with no-ff
f52c633 add merge
cf810e4 conflict fixed
5dc6824 & simple
14096d0 AND simple
b17d20e branch test
d46f35e remove test.txt
b84166e add test.txt
519219b git tracks changes
e43a48b understand how stage works
1094adb append GPL
e475afc add distributed
eaadf4e wrote a readme file
```

比方说要对`add merge`这次提交打标签，它对应的commit id是`f52c633`，敲入命令：

```shell
$ git tag v0.9 f52c633
```

再用命令`git tag`查看标签：

```shell
$ git tag
v0.9
v1.0
```

注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息：

```shell
$ git show v0.9
commit f52c63349bc3c1593499807e5c8e972b82c8f286 (tag: v0.9)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:56:54 2018 +0800

    add merge

diff --git a/readme.txt b/readme.txt
...
```

可以看到，`v0.9`确实打在`add merge`这次提交上。

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```shell
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

用命令`git show <tagname>`可以看到说明文字：

```shell
$ git show v0.1
tag v0.1
Tagger: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 22:48:43 2018 +0800

version 0.1 released

commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (tag: v0.1)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

diff --git a/readme.txt b/readme.txt
...
```

 注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。



### 小结

- 命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；

- 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；

- 命令`git tag`可以查看所有标签。

## 操作标签

如果标签打错了，也可以删除：

  ```shell
  $ git tag -d v0.1
  Deleted tag 'v0.1' (was f15b0dd)
  ```

因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

  ```shell
  $ git push origin v1.0
  Total 0 (delta 0), reused 0 (delta 0)
  To github.com:michaelliao/learngit.git
   * [new tag]         v1.0 -> v1.0
  ```

或者，一次性推送全部尚未推送到远程的本地标签：

  ```shell
  $ git push origin --tags
  Total 0 (delta 0), reused 0 (delta 0)
  To github.com:michaelliao/learngit.git
   * [new tag]         v0.9 -> v0.9
  ```

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

  ```shell
  $ git tag -d v0.9
  Deleted tag 'v0.9' (was f52c633)
  ```

然后，从远程删除。删除命令也是push，但是格式如下：

  ```shell
  $ git push origin :refs/tags/v0.9
  To github.com:michaelliao/learngit.git
   - [deleted]         v0.9
  ```

要看看是否真的从远程库删除了标签，可以登陆GitHub查看。

  ### 小结

  - 命令`git push origin <tagname>`可以推送一个本地标签；
  - 命令`git push origin --tags`可以推送全部未推送过的本地标签；
  - 命令`git tag -d <tagname>`可以删除一个本地标签；
  - 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

## 使用GitHub——fork

如何参与一个开源项目呢？比如人气极高的bootstrap项目，这是一个非常强大的CSS框架，你可以访问它的项目主页https://github.com/twbs/bootstrap，点“Fork”就在自己的账号下克隆了一个bootstrap仓库，然后，从自己的账号下clone：

```shell
git clone git@github.com:michaelliao/bootstrap.git
```

一定要从自己的账号下clone仓库，这样你才能推送修改。如果从bootstrap的作者的仓库地址`git@github.com:twbs/bootstrap.git`克隆，因为没有权限，你将不能推送修改。

Bootstrap的官方仓库`twbs/bootstrap`、你在GitHub上克隆的仓库`my/bootstrap`，以及你自己克隆到本地电脑的仓库，他们的关系就像下图显示的那样：

```ascii
┌─ GitHub ────────────────────────────────────┐
│                                             │
│ ┌─────────────────┐     ┌─────────────────┐ │
│ │ twbs/bootstrap  │────>│  my/bootstrap   │ │
│ └─────────────────┘     └─────────────────┘ │
│                                  ▲          │
└──────────────────────────────────┼──────────┘
                                   ▼
                          ┌─────────────────┐
                          │ local/bootstrap │
                          └─────────────────┘
```

如果你想修复bootstrap的一个bug，或者新增一个功能，立刻就可以开始干活，干完后，往自己的仓库推送。

如果你希望bootstrap的官方库能接受你的修改，你就可以在GitHub上发起一个pull request。当然，对方是否接受你的pull request就不一定了。

如果你没能力修改bootstrap，但又想要试一把pull request，那就Fork一下我的仓库：https://github.com/michaelliao/learngit，创建一个`your-github-id.txt`的文本文件，写点自己学习Git的心得，然后推送一个pull request给我，我会视心情而定是否接受。

### 小结

- 在GitHub上，可以任意Fork开源仓库；
- 自己拥有Fork后的仓库的读写权限；
- 可以推送pull request给官方仓库来贡献代码。

## 使用Gitee

使用GitHub时，国内的用户经常遇到的问题是访问速度太慢，有时候还会出现无法连接的情况（原因你懂的）。

如果我们希望体验Git飞一般的速度，可以使用国内的Git托管服务——[Gitee](https://gitee.com/?utm_source=blog_lxf)（[gitee.com](https://gitee.com/?utm_source=blog_lxf)）。

和GitHub相比，Gitee也提供免费的Git仓库。此外，还集成了代码质量检测、项目演示等功能。对于团队协作开发，Gitee还提供了项目管理、代码托管、文档管理的服务，5人以下小团队免费。

 Gitee的免费版本也提供私有库功能，只是有5人的成员上限。

Gitee企业版正在优惠中：

- 买1年送1个月：优惠码`lhkYPzFJ`
- 买2年送3个月：优惠码`YGyEBFZC`
- 买3年送6个月：优惠码`qBNwfvET`

[Gitee企业版专属购买通道 >>](https://gitee.com/enterprises?invite_code=Z2l0ZWUtMzgzMTE1)

小提示：记得先复制优惠码信息，在购买页面输入优惠码即可。

使用Gitee和使用GitHub类似，我们在Gitee上注册账号并登录后，需要先上传自己的SSH公钥。选择右上角用户头像 -> 菜单“修改资料”，然后选择“SSH公钥”，填写一个便于识别的标题，然后把用户主目录下的`.ssh/id_rsa.pub`文件的内容粘贴进去：

![gitee-add-ssh-key](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272158650.jpeg)

点击“确定”即可完成并看到刚才添加的Key：

![gitee-key](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272158177.jpeg)

如果我们已经有了一个本地的git仓库（例如，一个名为learngit的本地库），如何把它关联到Gitee的远程库上呢？

首先，我们在Gitee上创建一个新的项目，选择右上角用户头像 -> 菜单“控制面板”，然后点击“创建项目”：

![gitee-new-repo](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272158799.jpeg)

项目名称最好与本地库保持一致：

然后，我们在本地库上使用命令`git remote add`把它和Gitee的远程库关联：

```shell
git remote add origin git@gitee.com:liaoxuefeng/learngit.git

git remote add origin https://gitee.com/jzdxdd/gitskills.git
# git remote add origin git@github.com:michaelliao/learngit.git
# git remote add origin https://github.com/perfectddt/gitlearn.git
```

之后，就可以正常地用`git push`和`git pull`推送了！

### fatal: remote origin already exists.

如果在使用命令`git remote add`时报错：

```shell
git remote add origin git@gitee.com:liaoxuefeng/learngit.git
fatal: remote origin already exists.
```

这说明本地库已经关联了一个名叫`origin`的远程库，此时，可以先用`git remote -v`查看远程库信息：

```shell
git remote -v
origin	git@github.com:michaelliao/learngit.git (fetch)
origin	git@github.com:michaelliao/learngit.git (push)
```

可以看到，本地库已经关联了`origin`的远程库，并且，该远程库指向GitHub。

我们可以删除已有的GitHub远程库：

```shell
git remote rm origin
```

再关联Gitee的远程库（注意路径中需要填写正确的用户名）：

```shell
git remote add origin git@gitee.com:liaoxuefeng/learngit.git
```

此时，我们再查看远程库信息：

```shell
git remote -v
origin	git@gitee.com:liaoxuefeng/learngit.git (fetch)
origin	git@gitee.com:liaoxuefeng/learngit.git (push)
```

现在可以看到，origin已经被关联到Gitee的远程库了。通过`git push`命令就可以把本地库推送到Gitee上。

### 既关联GitHub，又关联Gitee

有的小伙伴又要问了，一个本地库能不能既关联GitHub，又关联Gitee呢？

答案是肯定的，因为git本身是分布式版本控制系统，可以同步到另外一个远程库，当然也可以同步到另外两个远程库。

使用多个远程库时，我们要注意，git给远程库起的默认名称是`origin`，如果有多个远程库，我们需要用不同的名称来标识不同的远程库。

仍然以`learngit`本地库为例，我们先删除已关联的名为`origin`的远程库：

```shell
git remote rm origin
```

然后，先关联GitHub的远程库：

```shell
git remote add github git@github.com:michaelliao/learngit.git
```

注意，远程库的名称叫`github`，不叫`origin`了。

接着，再关联Gitee的远程库：

```shell
git remote add gitee git@gitee.com:liaoxuefeng/learngit.git
```

同样注意，远程库的名称叫`gitee`，不叫`origin`。

现在，我们用`git remote -v`查看远程库信息，可以看到两个远程库：

```shell
git remote -v
gitee	git@gitee.com:liaoxuefeng/learngit.git (fetch)
gitee	git@gitee.com:liaoxuefeng/learngit.git (push)
github	git@github.com:michaelliao/learngit.git (fetch)
github	git@github.com:michaelliao/learngit.git (push)
```

如果要推送到GitHub，使用命令：

```shell
git push github master
```

如果要推送到Gitee，使用命令：

```shell
git push gitee master
```

这样一来，我们的本地库就可以同时与多个远程库互相同步：

```ascii
┌─────────┐ ┌─────────┐
│ GitHub  │ │  Gitee  │
└─────────┘ └─────────┘
     ▲           ▲
     └─────┬─────┘
           │
    ┌─────────────┐
    │ Local Repo  │
    └─────────────┘
```

Gitee也同样提供了Pull request功能，可以让其他小伙伴参与到开源项目中来。你可以通过Fork我的仓库：[https://gitee.com/liaoxuefeng/learngit](https://gitee.com/liaoxuefeng/learngit?utm_source=blog_lxf)，创建一个`your-gitee-id.txt`的文本文件， 写点自己学习Git的心得，然后推送一个pull request给我，这个仓库会在Gitee和GitHub做双向同步。

### gitee说明

我们强烈建议所有的git仓库都有一个`README`, `LICENSE`, `.gitignore`文件

### 简易的命令行入门教程:

Git 全局设置:

```shell
git config --global user.name "卡噗熊熊"
git config --global user.email "9073080+jzdxdd@user.noreply.gitee.com"
```

创建 git 仓库:

```shell
mkdir gitskills
cd gitskills
git init 
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/jzdxdd/gitskills.git
git push -u origin "master"
```

已有仓库?

```shell
cd existing_git_repo
git remote add origin https://gitee.com/jzdxdd/gitskills.git
git push -u origin "master"
```

# 自定义Git

在[安装Git](https://www.liaoxuefeng.com/wiki/896043488029600/896067074338496)一节中，我们已经配置了`user.name`和`user.email`，实际上，Git还有很多可配置项。

比如，让Git显示颜色，会让命令输出看起来更醒目：

```shell
$ git config --global color.ui true
```

这样，Git会适当地显示不同的颜色，比如`git status`命令：

![git-color](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272158327.png)

文件名就会标上颜色。

## 忽略特殊文件

有些时候，你必须把某些文件放到Git工作目录中，但又不能提交它们，比如保存了数据库密码的配置文件啦，等等，每次`git status`都会显示`Untracked files ...`，有强迫症的童鞋心里肯定不爽。

好在Git考虑到了大家的感受，这个问题解决起来也很简单，在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：https://github.com/github/gitignore

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

举个例子：

假设你在Windows下进行Python开发，Windows会自动在有图片的目录下生成隐藏的缩略图文件，如果有自定义目录，目录下就会有`Desktop.ini`文件，因此你需要忽略Windows自动生成的垃圾文件：

```shell
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini
```

然后，继续忽略Python编译产生的`.pyc`、`.pyo`、`dist`等文件或目录：

```shell
# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build
```

加上你自己定义的文件，最终得到一个完整的`.gitignore`文件，内容如下：

```shell
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa
```

最后一步就是把`.gitignore`也提交到Git，就完成了！当然检验`.gitignore`的标准是`git status`命令是不是说`working directory clean`。

使用Windows的童鞋注意了，如果你在资源管理器里新建一个`.gitignore`文件，它会非常弱智地提示你必须输入文件名，但是在文本编辑器里“保存”或者“另存为”就可以把文件保存为`.gitignore`了。

有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被`.gitignore`忽略了：

```shell
$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
Use -f if you really want to add them.
```

如果你确实想添加该文件，可以用`-f`强制添加到Git：

```shell
$ git add -f App.class
```

或者你发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

```shell
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
```

Git会告诉我们，`.gitignore`的第3行规则忽略了该文件，于是我们就可以知道应该修订哪个规则。

还有些时候，当我们编写了规则排除了部分文件时：

```shell
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class
```

但是我们发现`.*`这个规则把`.gitignore`也排除了，并且`App.class`需要被添加到版本库，但是被`*.class`规则排除了。

虽然可以用`git add -f`强制添加进去，但有强迫症的童鞋还是希望不要破坏`.gitignore`规则，这个时候，可以添加两条例外规则：

```shell
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class

# 不排除.gitignore和App.class:
!.gitignore
!App.class
```

把指定文件排除在`.gitignore`规则外的写法就是`!`+文件名，所以，只需把例外文件添加进去即可。

可以通过[https://gitignore.itranswarp.com](https://gitignore.itranswarp.com/)在线生成`.gitignore`文件。

### 小结

- 忽略某些文件时，需要编写`.gitignore`；
- `.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！

## 配置别名

有没有经常敲错命令？比如`git status`？`status`这个单词真心不好记。

如果敲`git st`就表示`git status`那就简单多了，当然这种偷懒的办法我们是极力赞成的。

我们只需要敲一行命令，告诉Git，以后`st`就表示`status`：

```shell
$ git config --global alias.st status
```

好了，现在敲`git st`看看效果。

当然还有别的命令可以简写，很多人都用`co`表示`checkout`，`ci`表示`commit`，`br`表示`branch`：

```shell
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
```

以后提交就可以简写成：

```shell
$ git ci -m "bala bala bala..."
```

`--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。

在[撤销修改](https://www.liaoxuefeng.com/wiki/896043488029600/897889638509536)一节中，我们知道，命令`git reset HEAD file`可以把暂存区的修改撤销掉（unstage），重新放回工作区。既然是一个unstage操作，就可以配置一个`unstage`别名：

```shell
$ git config --global alias.unstage 'reset HEAD'
```

当你敲入命令：

```shell
$ git unstage test.py
```

实际上Git执行的是：

```shell
$ git reset HEAD test.py
```

配置一个`git last`，让其显示最后一次提交信息：

```shell
$ git config --global alias.last 'log -1'
```

这样，用`git last`就能显示最近一次的提交：

```shell
$ git last
commit adca45d317e6d8a4b23f9811c3d7b7f0f180bfe2
Merge: bd6ae48 291bea8
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Thu Aug 22 22:49:22 2013 +0800

    merge & fix hello.py
```

甚至还有人丧心病狂地把`lg`配置成了：

```shell
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

来看看`git lg`的效果：

![git-lg](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272156590.png)

为什么不早点告诉我？别激动，咱不是为了多记几个英文单词嘛！

### 配置文件

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在`.git/config`文件中：

```
$ cat .git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:michaelliao/learngit.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[alias]
    last = log -1
```

别名就在`[alias]`后面，要删除别名，直接把对应的行删掉即可。

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

```
$ cat .gitconfig
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
[user]
    name = Your Name
    email = your@email.com
```

配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。

### 小结

给Git配置好别名，就可以输入命令时偷个懒。我们鼓励偷懒。

## 搭建Git服务器

最后更新: 2018/2/26 21:47 / 阅读: 74524215

------

在[远程仓库](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)一节中，我们讲了远程仓库实际上和本地仓库没啥不同，纯粹为了7x24小时开机并交换大家的修改。

GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的`apt`命令就可以完成安装。

假设你已经有`sudo`权限的用户账号，下面，正式开始安装。

第一步，安装`git`：

```
$ sudo apt-get install git
```

第二步，创建一个`git`用户，用来运行`git`服务：

```
$ sudo adduser git
```

第三步，创建证书登录：

收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。

第四步，初始化Git仓库：

先选定一个目录作为Git仓库，假定是`/srv/sample.git`，在`/srv`目录下输入命令：

```
$ sudo git init --bare sample.git
```

Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以`.git`结尾。然后，把owner改为`git`：

```
$ sudo chown -R git:git sample.git
```

第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑`/etc/passwd`文件完成。找到类似下面的一行：

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改为：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样，`git`用户可以正常通过ssh使用git，但无法登录shell，因为我们为`git`用户指定的`git-shell`每次一登录就自动退出。

第六步，克隆远程仓库：

现在，可以通过`git clone`命令克隆远程仓库了，在各自的电脑上运行：

```
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

剩下的推送就简单了。

### 管理公钥

如果团队很小，把每个人的公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用[Gitosis](https://github.com/res0nat0r/gitosis)来管理公钥。

这里我们不介绍怎么玩[Gitosis](https://github.com/res0nat0r/gitosis)了，几百号人的团队基本都在500强了，相信找个高水平的Linux管理员问题不大。

### 管理权限

有很多不但视源代码如生命，而且视员工为窃贼的公司，会在版本控制系统里设置一套完善的权限控制，每个人是否有读写权限会精确到每个分支甚至每个目录下。因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制。不过，因为Git支持钩子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。[Gitolite](https://github.com/sitaramc/gitolite)就是这个工具。

这里我们也不介绍[Gitolite](https://github.com/sitaramc/gitolite)了，不要把有限的生命浪费到权限斗争中。

### 小结

- 搭建Git服务器非常简单，通常10分钟即可完成；
- 要方便管理公钥，用[Gitosis](https://github.com/res0nat0r/gitosis)；
- 要像SVN那样变态地控制权限，用[Gitolite](https://github.com/sitaramc/gitolite)。

# 使用SourceTree

当我们对Git的提交、分支已经非常熟悉，可以熟练使用命令操作Git后，再使用GUI工具，就可以更高效。

Git有很多图形界面工具，这里我们推荐[SourceTree](https://www.sourcetreeapp.com/)，它是由[Atlassian](https://www.atlassian.com/)开发的免费Git图形界面工具，可以操作任何Git库。

首先从[官网](https://www.sourcetreeapp.com/)下载SourceTree并安装，然后直接运行SourceTree。

第一次运行SourceTree时，SourceTree并不知道我们的Git库在哪。如果本地已经有了Git库，直接从资源管理器把文件夹拖拽到SourceTree上，就添加了一个本地Git库：

![add-local-repo](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155447.jpeg)

也可以选择“New”-“Clone from URL”直接从远程克隆到本地。

## 提交

我们双击`learngit`这个本地库，SourceTree会打开另一个窗口，展示这个Git库的当前所有分支以及文件状态。选择左侧面板的“WORKSPACE”-“File status”，右侧会列出当前已修改的文件（Unstaged files）：

![unstaged](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155801.png)

选中某个文件，该文件就自动添加到“Staged files”，实际上是执行了`git add README.md`命令：

![add](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155117.png)

然后，我们在下方输入Commit描述，点击“Commit”，就完成了一个本地提交：

![commit](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155487.png)

实际上是执行了`git commit -m "update README.md"`命令。

使用SourceTree进行提交就是这么简单，它的优势在于可以可视化地观察文件的修改，并以红色和绿色高亮显示。

## 分支

在左侧面板的“BRANCHES”下，列出了当前本地库的所有分支。当前分支会加粗并用○标记。要切换分支，我们只需要选择该分支，例如`master`，然后点击右键，在弹出菜单中选择“Checkout master”，实际上是执行命令`git checkout master`：

![checkout](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155658.png)

要合并分支，同样选择待合并分支，例如`dev`，然后点击右键，在弹出菜单中选择“Merge dev into master”，实际上是执行命令`git merge dev`：

![merge-dev-into-master](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155248.png)

## 推送

在SourceTree的工具栏上，分别有`Pull`和`Push`，分别对应命令`git pull`和`git push`，只需注意本地和远程分支的名称要对应起来，使用时十分简单。

注意到使用SourceTree时，我们只是省下了敲命令的麻烦，SourceTree本身还是通过Git命令来执行任何操作。如果操作失败，SourceTree会自动显示执行的Git命令以及错误信息，我们可以通过Git返回的错误信息知道出错的原因：

![push-error](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155076.png)

## 小结

使用SourceTree可以以图形界面操作Git，省去了敲命令的过程，对于常用的提交、分支、推送等操作来说非常方便。

SourceTree使用Git命令执行操作，出错时，仍然需要阅读Git命令返回的错误信息。



# 同时将代码备份到Gitee和GitHub

如何将GitHub项目一步导入Gitee
如何保持Gitee和GitHub同步更新

## 如何将GitHub项目一步导入Gitee

### 方法一：

登陆 Gitee 账号，点击右上角的 + 号，点击「从 GitHub 导入项目」，在跳转的页面中授权 Gitee 访问。
![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155902.png)

然后选择性的从GitHub中导入
![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155149.png)

### 方法二：

在创建项目的时候，选择导入已有项目。

如果是私有项目将会需要输入 GitHub 的账号信息。

## 如何保持Gitee和GitHub同步更新

### 方式一: 使用Gitee的强制同步

![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155931.png)

我还是只用维护 github 那份源码, gitee 这边没忘记的话, 手搓点击下强制同步按钮即可。

但是容易忘记, 造成两边不完全同步。

切记，只有从GitHub上导入的仓库才有强制同步的按钮

### 方式二: 手搓 push 多次

那么不外乎就是配置多个远程库地址, 多次推送咯, 那么我们先来看看现有远程库的情况（以:WhiteHole

```bash
$ git remote --verbose
origin  https://gitee.com/BlackThompson/white-hole.git (fetch)
origin  https://gitee.com/BlackThompson/white-hole.git (push)
```

可以看到目前仅有 https://gitee.com/BlackThompson/white-hole.git 这个远程库地址.

我们来加一个 gitee 的远程地址, 首先在 gitee 建好同步仓库, 然后我们在本地添加一个新的远程库地址:

```bash
$ git remote add githuborigin https://github.com/BlackThompson/WhiteHole
```

添加完成后我们查看一下:

```bash
$ git remote --verbose
githuborigin    https://github.com/BlackThompson/WhiteHole (fetch)
githuborigin    https://github.com/BlackThompson/WhiteHole (push)
origin  https://gitee.com/BlackThompson/white-hole.git (fetch)
origin  https://gitee.com/BlackThompson/white-hole.git (push)
```

可以查看到以下2个远程库地址:

githuborigin: 是我们新加的 github 的远程库地址
origin: 是我们之前在 gitee 的远程库地址
接下来同步:

```bash
git add .
git commit -m "add gitee"
git push -u origin master
git push -u githuborigin master
```

比之前多个一次 git push 操作…其他和之前没有太大区别…没有更多的心智负担.

但是经常容易忘记…

### 方式三: 最多跑一次

不想着法偷懒的 coder 不是好程序员, 秉承 “最多跑一次” 的理念, 让我们试试怎么一次 push 统统搞定.

在本地 git 仓库里找到这个文件 .git/config, 内容如下:
![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155919.png)

```bash
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = https://gitee.com/BlackThompson/white-hole.git	
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
[gui]
	wmstate = normal
	geometry = 1205x669+38+38 276 304
[remote "githuborigin"]
	url = https://github.com/BlackThompson/WhiteHole
	fetch = +refs/heads/*:refs/remotes/githuborigin/*
```

改为如下:

合并2个 remote 配置

```bash
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = https://github.com/BlackThompson/WhiteHole
	url = https://gitee.com/BlackThompson/white-hole.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```

上面这个手动配置是为了更好的说明而已, 其实可以用以下命令简化操作, 在 origin 节点下补充了一个新的远程地址.

```bash
$ git remote set-url --add origin https://github.com/BlackThompson/WhiteHole
```

看看补充后的远程地址情况

```bash
$ git remote --verbose
origin  https://gitee.com/BlackThompson/white-hole.git (fetch)
origin  https://gitee.com/BlackThompson/white-hole.git (push)
origin  https://github.com/BlackThompson/WhiteHole (push)
<<<<<<< HEAD
# 以后约定这样
giteeorigin     https://gitee.com/jzdxdd/gitskills.git (fetch)
giteeorigin     https://gitee.com/jzdxdd/gitskills.git (push)
githuborigin    https://github.com/perfectddt/gitskills.git (fetch)
githuborigin    https://github.com/perfectddt/gitskills.git (push)
origin  https://gitee.com/jzdxdd/gitskills.git (fetch)
origin  https://gitee.com/jzdxdd/gitskills.git (push)
origin  https://github.com/perfectddt/gitskills.git (push)
=======
>>>>>>> parent of a87d026 (giteeorigin + githuborigin sametime push->origin)
```

注意看后面的 (fetch)(push), 相信你会明白点什么.

然后我们可以继续这样使用来实现 github & gitee 的同步推送和分发:

```bash
git add .
git commit -m "github & gitee 同步推送和分发"
git push origin master
```

可以看到, 使用上和最初没有任何区别, 只是多配置了一次, 算是实现了 “最多配(跑)一次”.

方式四：命令行把GitHub上的代码拉取导入Gitee
如果是本地仓库，只在需要命令行添加用不同名称标识的 Gitee 和 Github 远程库。

git remote add 远程库名 远程库地址
![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304272155911.png)

具体方法操作如下：

1、首先通过 git remote -v 查看您要同步的仓库的远程库列表，如果在列表中没有您码云的远程库地址，您需要新增一个地址

>   git remote add 远程库名 远程库地址
>   eg: git remote add gitee git@github.com:xxx/xxx.git

>   如果在 add 的时候出现error: Could not remove config section ‘remote.xxx’.一类的错误，通过把仓库下.git/config 文件里面的 [remote “xxx”] 删掉或者是用别的远程库名即可。

2、从GitHub上拉取最新代码到本地

>   git pull 远程库名 分支名
>   eg：git pull origin master

3、推送本地最新代码到码云上

>   git push 远程库名 分支名
>   eg：git push gitee master

如果出现有差异的话需要自己手动解决差异

参考文章：

[如何将 GitHub 项目导入码云？一步搞定！](https://blog.gitee.com/2018/06/05/github_to_gitee/)

[如何同步多个 git 远程仓库](https://www.cnblogs.com/taadis/p/12170953.html)

# Git 删除某一次提交

## 一、 git reset

-   git reset ：回滚到某次提交。
-   git reset --soft：此次提交之后的修改会被退回到暂存区
-   git reset --hard：此次提交之后的修改不做任何保留，git status 查看工作区是没有记录的。

1.  回滚代码
    如果需要删除的 commit 是最新的，那么可以通过 git reset 命令将代码回滚到之前某次提交的状态，但一定要将现有的代码做好备份，否则回滚之后这些变动都会消失。具体操作如下：



```cpp
1. git log // 查询要回滚的 commit_id
2. git reset --hard commit_id // HEAD 就会指向此次的提交记录
3. git push origin HEAD --force // 强制推送到远端
```

1.  误删恢复
    如果回滚代码之后发现复制错了 commit_id，或者误删了某次 commit 记录，也可以通过下方代码恢复：



```bash
1. git relog // 复制要恢复操作的前面的 hash 值
2. git reset --hard hash // 将 hash 换成要恢复的历史记录的 hash 值
```

1.  注意：删除中间某次提交时最好不要用 git reset 回退远程库，因为之后其他人提交代码时用 git pull 也会把自己的本地仓库回退到之前的版本，容易出现差错进而增加不必要的工作量。

## 二、git rebase

-   git rebase：当两个分支不在一条线上，需要执行 merge 操作时使用该命令。

1.  撤销提交
    如果中间的某次 commit 需要删除，可以通过 git rebase 命令实现，方法如下：

```go
1. git log // 查找要删除的前一次提交的 commit_id
2. git rebase -i commit_id // 将 commit_id 替换成复制的值
3. 进入 Vim 编辑模式，将要删除的 commit 前面的 `pick` 改成 `drop`
4. 保存并退出 Vim
```

这样就完成了。

1.  解决冲突
    该命令执行时极有可能出现 reabase 冲突，可以通过以下方法解决：

```csharp
1. git diff // 查看冲突内容
2. // 手动解决冲突（冲突位置已在文件中标明）
3. git add <file> 或 git add -A // 添加
4. git rebase --continue // 继续 rebase
5. // 若还在 rebase 状态，则重复 2、3、4，直至 rebase 完成出现 applying 字样
6. git push
```

## git rebase合并同一个分支的多个提交

### 背景

开发过程中，本地时长有无数次的commit，有时候我们想合并功能相同的commit，保持历史记录的干净整洁

### git rebase



```ruby
# 从HEAD版本开始往过去数3个版本
$ git rebase -i HEAD~3

# 合并指定版本号（不包含此版本）
$ git rebase -i [commitid]
```

参数说明：

-   -i进入交互模式
-   commitid 指定一个commitid，则交互界面中只会列出该commitid之后的所有提交，不包含它本身
    交互界面命令解释：



```objectivec
# 命令:
# p, pick <提交> = 使用提交
# r, reword <提交> = 使用提交，但修改提交说明
# e, edit <提交> = 使用提交，进入 shell 以便进行提交修补
# s, squash <提交> = 使用提交，但融合到前一个提交
# f, fixup <提交> = 类似于 "squash"，但丢弃提交说明日志
# x, exec <命令> = 使用 shell 运行命令（此行剩余部分）
# b, break = 在此处停止（使用 'git rebase --continue' 继续变基）
# d, drop <提交> = 删除提交
```

### 操作步骤

#### 查看log

git的log是从上到下依次是从新到旧的提交



```csharp
$ git log --oneline
291e427 update website
8c8f3f4 update website
1693a6f update clear-logs.sh version
3759b84 update clear-logs.sh
fc36a2a add links
1d795e6 fix && update clear-logs.sh 0.0.2
9536dab add dingtalk script
3a51aaa fix shellcheck problem
2db6ad3 add clear logs scripts
e57b0e6 fix && add batch del
17cb931 fix && add batch del
cf7e875 add redis script
fe4bbcb Initial commit
```

#### 编辑要合并的版本



```python
# 指定要合并版本号，cf7e875 不参与合并，进入 vi 编辑器
$ git rebase -i cf7e875 
pick 17cb931 fix && add batch del
pick e57b0e6 fix && add batch del
pick 2db6ad3 add clear logs scripts
pick 3a51aaa fix shellcheck problem
pick 9536dab add dingtalk script
pick 1d795e6 fix && update clear-logs.sh 0.0.2
pick fc36a2a add links
pick 3759b84 update clear-logs.sh
pick 1693a6f update clear-logs.sh version
pick 8c8f3f4 update website

# Rebase cf7e875..291e427 onto cf7e875 (10 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

编辑commit信息



```csharp
# 把 e57b0e6 合并到 17cb931，不打印提交说明的日志；把 1693a6f 合并到 3759b84
pick 17cb931 fix && add batch del
f e57b0e6 fix && add batch del
pick 2db6ad3 add clear logs scripts
pick 3a51aaa fix shellcheck problem
pick 9536dab add dingtalk script
pick 1d795e6 fix && update clear-logs.sh 0.0.2
pick fc36a2a add links
pick 3759b84 update clear-logs.sh
s 1693a6f update clear-logs.sh version
pick 8c8f3f4 update website
```

然后`:wq`退出vim编辑器，此是就是注释界面



```bash
# This is a combination of 2 commits.
# This is the 1st commit message:

update clear-logs.sh

# This is the commit message #2:

update clear-logs.sh version

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Tue Jul 28 18:25:57 2020 +0800
#
# interactive rebase in progress; onto cf7e875
# Last commands done (9 commands done):
#    pick 3759b84 update clear-logs.sh
#    s 1693a6f update clear-logs.sh version
# Next command to do (1 remaining command):
#    pick 8c8f3f4 update website
# You are currently editing a commit while rebasing branch 'master' on 'cf7e875'.
#
# Changes to be committed:
# modified:   logs/README.md
# modified:   logs/clear-logs.sh
```

可以编辑commit message，保存并退出(`:wq`)，即可完成commit合并



```css
update clear-logs.sh
```

### 查看合并后的log



```csharp
$ git log --oneline
47e7751 update website
4c2316c update clear-logs.sh
73f082e add links
56adcf2 fix && update clear-logs.sh 0.0.2
ebf3786 add dingtalk script
6e81ea7 fix shellcheck problem
64ca58f add clear logs scripts
9327def fix && add batch del
cf7e875 add redis script
fe4bbcb Initial commit
```

然后就可以将整理过后的commit推送到远程了

#### 冲突解决

在 `git rebase` 的时候可能会出现冲突，此时就需要解决冲突
错误提示信息：`git rebase -i resumeerror: could not apply ...`。

```ruby
# 查看冲突
$ git status

# 解决冲突之后，本地提交
$ git add .

# rebase 继续
$ git rebase --continue
```



## 三、 git revert

-   git revert：放弃某次提交。
    git revert 之前的提交仍会保留在 git log 中，而此次撤销会做为一次新的提交。
-   git revert -m：用于对 merge 节点的操作，-m 指定具体某个提交点。

1.  撤销提交
    要撤销中间某次提交时，使用 git revert 也是一个很好的选择：



```cpp
1. git log // 查找需要撤销的 commit_id
2. git revert commit_id  // 撤销这次提交
```

1.  撤销 merge 节点提交
    如果这次提交是 merge 节点的话，则需要加上 -m 指令：



```csharp
1. git revert commit_id -m 1 // 第一个提交点
2. // 手动解决冲突
3. git add -A
4. git commit -m ""
5. git revert commit_id -m 2 // 第二个提交点
6. // 重复 2，3，4
7. git push
```

# 关于 Git 大文件上传这件小事

## 提交前的未雨绸缪

其实，博主不愿意在 Git 仓库里上传 Word 或者 Excel 文件，一个最为直接的理由是，它会成为我们拉取或者推送代码时的累赘。君不见，腾讯硬生生在手机 QQ 里内置了一个虚幻 4 引擎，想象一下，如果把这么多的文件都放到 Git 仓库里，每次做一点修改该有多痛苦啊！事实上，Github 对文件大小的限制是 100M，Gitlab 对文件大小的限制则是 600M，一旦超过这个限制，就会被判定为大文件。因此，[Atlassian](https://www.atlassian.com/)、[GitHub](https://github.com/) 等组织一起开发了针对 Git 的 Large File Storage 扩展，即：[Git LFS](https://git-lfs.github.com/)。其原理是延迟地下载大文件的相关版本来减少大文件对仓库的影响，具体来说，就是在 `checkout` 到工作区的时候才会真正去下载大文件的内容。如果大家想了解更多 [Git LFS](https://git-lfs.github.com/) 的细节，可以阅读下面这份文档：https://www.atlassian.com/git/tutorials/git-lfs，这里不再考虑对其进行二次加工。



![img](https://pic2.zhimg.com/80/v2-646b1dafb124fc1f52752742af5f1799_720w.webp)

Git LFS 原理示意图



当你准备向一个 Git 仓库提交大文件的时候，首先，你需要下载和安装 [Git LFS](https://git-lfs.github.com./) 扩展并执行命令：

```bash
git lfs install
```

其次，在 Git 仓库中，你需要通过 `git lfs track` 命令告诉 Git LFS，你希望它帮你管理哪些文件：

```bash
git lfs track "*.jpg"
```

这里以 `*.jpg` 为例，它表示希望 Git LFS 对所有的 `.jpg` 格式的文件进行管理，这一步的结果是生成一个名为 `.gitattributes` 的文件。因此，理论上你可以直接编辑这个文件来达到相同的目的：

```bash
*.jpg filter=lfs diff=lfs merge=lfs -text
```

最后，你需要确保 `.gitattributes` 文件能被 Git 仓库追踪：

```bash
git add .gitattributes
```

一旦完成了这个准备工作，你就可以像平时一样提交和推送变更：

```bash
git add awesome.jpg
git commit -m "Add a awesome image file"
git push origin main
```

下图演示了 Git LFS 从初始化到应用的整个过程，可以注意到，这个过程中会生成各种钩子脚本：



![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304291431264.webp)

Git LFS 的初始化与应用



## 提交后的亡羊补牢

扁鹊见蔡桓公的故事，告诉我们防微杜渐的道理。可如果你和博主一样，是在提交了一个大文件以后，才开始接触 [Git LFS](https://git-lfs.github.com/) 的相关内容。那么，你大概会遇到下面的错误：



![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304291431269.webp)

Git 上传文件超过 100M 的错误提示



你可能会想，既然是这个文件的大小超过了 Github 的限制，那我把这个文件删除掉是不是就可以了呢？经过博主一番挣扎，发现这个思路完全是徒劳的。因为这个大文件就像持续了三年的新冠病毒一样，只要存在被它感染过的历史记录，都会在推送时提示上面的错误。所以，删除这个大文件并不能真正解决问题，我们必须要确保此前的提交历史中没有这个大文件。下面分享一个神奇的命令 [git filter-branch](https://cloud.tencent.com/developer/section/1138641) ：

```text
git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch /src/CSharpWasm/mono-6.12.0.182-x64-0.msi'
```

通过这个命令，我们就可以重写整个提交历史：



![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304291431592.webp)

从提交历史中删除指定的大文件



此时，我们只需要按提交大文件前的步骤，重新提交该文件即可，并且可以突破 100M 的限制：



![img](https://jzdxdd.oss-cn-hangzhou.aliyuncs.com/img202304291431228.webp)

成功提交一个超过 100M 的大文件

注意：从git lfs install开始，add 和commit都得重新来

## 本文小结

我写作的话题，基本上可以分为两类：有计划的和无计划的。显然，这一篇属于后者，它就像育碧旗下的开放世界，当你漫游其中总会遇到那些令人意外的支线任务。我个人挺喜欢这种由点及面的认知模式，即从一个特定的问题逐步过渡到一个更为宽泛的知识或者体系上面。在这篇文章里，无法提交一个超过 100M 的大文件是表，不了解 [Git LFS](https://git-lfs.github.com/) 是里，更有趣的一点是，解决问题的过程通常是由“**果**”反推出“**因**”，而撰写博客的过程则是由“**因**”顺推出“**果**”。从这个角度来看的话，对读者“**揣着明白装糊涂**”这才是最难把握的一个分寸，平时写长文章总担心写不清楚，而写短文章则担心像流水账。对于 Git LFS 来说，Git 仓库存储的其实是一个[指针文件](https://github.com/git-lfs/git-lfs/wiki/Tutorial#lfs-pointer-files-advanced)，真正的内容则是存储在 LFS 服务器里，主流的代码托管平台如 Github、Gitlab 等都支持 Git LFS，我们只需要安装 Git LFS 的客户端扩展即可。当然，我对非文本文件的合并依然持悲观态度，想象一下，两个人同时修改了一个大文件，一个已经推送到远程，一个已经提交到本地，那么，当他们尝试合并代码的时候，又会发生什么事情呢？作为一名程序员，每天被安排排查和解决问题，简直是家常便饭，可解决问题会有尽头吗？我想，大概率是没有的罢！
