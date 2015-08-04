Git专家之路
==========
Git这个在只有几天之内写出来的源代码管理工具，如今发展成了苍天大树，大量的开源项目已经放弃
传统的svn或cvs，转入git的阵营。通过该文档，你可以了解到git的工作原理，还有git的专家级的
使用方法，从而提高您的工作效率。

Git工作原理
======================
传统的SCM工作原理
--------------------
使用过svn或者perforce的都知道，你必须要有一个搭建一个服务器，才能让你的代码使用版本控制工具。
一旦开始使用这个服务器，您想切换到另外的服务器就非常麻烦。如果服务器down了， 你的commit就不能
分享给别人，大家只能干等着服务器修好后，你的commit提交了才能基于你的工作继续。 所有传统的版本控制
工具就是典型的客户端－服务器结构。一切操作都要与服务器同步。在提交后，如果你觉得你的提交不正确，
那么你只能创建一个新的提交从而无法编辑之前的提交。

git的单机模式
---------------------
git使用了一个全新的方式，首先，使用GIT，你不需要搭建服务器。你可以把Git想想成单机
程序，无需联网，你就可以使用版本控制。这样你就可以非常灵活的在本地修改代码，提交代码。比如
你自己做了一个项目，但是你没有源代码服务器，你只想在你本地进行开发，这时你可以使用git，git作为
单机程序，在你对源代码进行版本控制时，你可以：

* 提交代码
* 回滚代码
* 创建分支开发测试新的功能
* 切换分支
* 查看提交历史
* 删除/合并/修正提交

总之，在单机模式下你可以使用服务器模式下的所有功能，并获得很多传统Git没有的功能，
所以我们可以先git当成一个强大的
单机软件，这个单机软件做到了那些传统的SCM无法做到的事情。

Git的联网模式
-----------------------
Git联网模式协议
------------------------------
Ok，在了解了git强大的单机模式后更强大的联网功能。那问题来了，git联网功能使用什么协议呢？
git的联网功能支持很多协议，包括：

* 本地协议
* SSH 协议
* HTTP/HTTPS 协议
* GIT 自有协议

先来说说本地协议。 本地协议就是指通过直接文件的交换协议。据个例子，假如你和你同事在开发同一
个项目，都没有联网。那怎么使用Git来同步代码呢？我们可以首先将同事A的源代码目录拷贝到U盘里，然后
把插到同事B的代码上。同事B就可以使用Git命令U盘里的数据同步到自己的工作区里了。你可以使用和其他协议
一样的命令将U盘里的代码同步过来，例如：

    git remote add origin /mount/U-Disk/Path/To/Git/Diretory
    git pull origin
    git push origin

除了本地协议，ssh协议是另外一个常用的协议。git可以使用ssh来进行通信，
从而完成代码合并和提交。这样我们可以充分的利用ssh协议的鉴权和安全认证，从而简化服务器搭建过程。

Http协议和Https协议会相当于比较麻烦，因为你需要设置一个web server，尽管有一些现成的，
但是还要研究如何使用这个会耽误一定时间。

另外也可以使用git自有协议。目前git提供了git-daemon命令，启动它时，他会监听9418端口，从而
可以使git变成服务器。但git-daemon没有提供较好的鉴权认证服务。所以不建议使用。端口9418也
容易被防火墙拦截。

如何使用Git联网模式
------------------------------
了解了git的联网模式协议，下一步我们来看看如何使用git的联网模式。也就是如何让git与服务器端
“交流”。在git中称为“上游”（Upstream）。不同于与传统的SCM，Git可以添加多个“上游”，默认的
上游名称为“origin”。当然你也可以变成其他的名字。对于local来说，它可以从任何“上游”拉取
代码（pull），也可以将自己的本地变化（commmit）推送（push）任何一个上游（upstream）。

那如何使用git添加多个上游呢？
为了简化我们的例子， 我们假设我们在本地有三个git的project，为了创建，你可以使用如下命令：

    git init offical
    git init teamA
    git init myVersion

现在我们执行命令，为myVersion添加两个upStream：

    git remote add -t master teamA /Users/ajing/test/teamA/.git
    git remote add -t master offical /Users/ajing/test/offical/.git

添加完了之后，我们理论上就能够从teamA和offical里pull代码和push代码了。先说怎么来pull，
因为offical和teamA都是空的，所以我们先在offical里面添加一个commit：

    ➜  offical git:(master) vi offical.txt
    ➜  offical git:(master) ✗ git add offical.txt
    ➜  offical git:(master) ✗ git commit -m "add offical.txt"
    [master (root-commit) fe52848] add offical.txt
     1 file changed, 1 insertion(+)
     create mode 100644 offical.txt

现在进入myVersion，就可以pull到这个change了：

    git pull offical

当然，在teamA里添加一个commit后，也可以pull到teamA的commit：

    git pull teamA

说完pull，再说push。比如我在这边myVersion里加了一个文件叫myVersion.txt:

    ➜  myVersion git:(master) vi myversion.txt
    ➜  myVersion git:(master) ✗ git add myversion.txt
    ➜  myVersion git:(master) ✗ git commit -m "add myversion"
    [master 5393e0d] add myversion
     1 file changed, 1 insertion(+)
     create mode 100644 myversion.txt

如果直接执行git push，会出现如下错误：

    fatal: No configured push destination.
    Either specify the URL from the command-line or configure a remote repository using

        git remote add <name> <url>

    and then push using the remote name

        git push <name>

原因很简单，我们在push的时候没有指定要push到哪个upstream，而且我们也没有设置默认的
upstream。 所以需要使用如下命令：

    git push offical master

但是会出现如下的错误：

    Counting objects: 10, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (7/7), done.
    Writing objects: 100% (10/10), 1.05 KiB | 0 bytes/s, done.
    Total 10 (delta 0), reused 0 (delta 0)
    remote: error: refusing to update checked out branch: refs/heads/master
    remote: error: By default, updating the current branch in a non-bare repository
    remote: error: is denied, because it will make the index and work tree inconsistent
    remote: error: with what you pushed, and will require 'git reset --hard' to match
    remote: error: the work tree to HEAD.
    remote: error:
    remote: error: You can set 'receive.denyCurrentBranch' configuration variable to
    remote: error: 'ignore' or 'warn' in the remote repository to allow pushing into
    remote: error: its current branch; however, this is not recommended unless you
    remote: error: arranged to update its work tree to match what you pushed in some
    remote: error: other way.
    remote: error:
    remote: error: To squelch this message and still keep the default behaviour, set
    remote: error: 'receive.denyCurrentBranch' configuration variable to 'refuse'.
    To /Users/ajing/test/offical/.git
     ! [remote rejected] master -> master (branch is currently checked out)
    error: failed to push some refs to '/Users/ajing/test/offical/.git'

出现上述错误，是因为没有offical的HEAD指向的master，如果这个push上去，对于remote那边，就
不知道该怎么办了。所有需要将offical的HEAD切换到另外的branch上，这
个问题就可以避免了：

    ➜  offical git:(master) git branch temp
    ➜  offical git:(master) git checkout temp
    Switched to branch 'temp'

现在再执行git push offical master:

    myVersion git:(master) git push offical master
    Counting objects: 10, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (7/7), done.
    Writing objects: 100% (10/10), 1.05 KiB | 0 bytes/s, done.
    Total 10 (delta 0), reused 0 (delta 0)
    To /Users/ajing/test/offical/.git
   87ee6a0..5393e0d  master -> master

同样的，你可以针对另外的一个branch进行一样的操作，以将你的commit push到teamA中。

Git Branch操作
-----------------------------
上一节，我们重点分析了git的工作原理，和如何让git跟多个server互动。这一节，我们重点分析
如何进行分支管理。“增删查改”是数据库操作的几个重要操作，这个同样也适用于Git的操作，针对Branch，我们看看是如何
进行增删查改的。
要查询当前有哪些分支，直接输入

    git branch
    git branch -r # 查看远程分支

要查看某个branch的具体状态commit log, 还有不同branch之间的区别， 可以使用如下命令：

    git status new-branch
    git log new-branch
    git diff branch1 branch2


创建branch很简单，只需要执行如下命令：
    Usage：
    git branch [--set-upstream | --track | --no-track] [-l] [-f] <branchname> [<start-point>]
    Example:
    git branch master-1 master^

其中branchname是新的branch name， start－point是当前branch是从哪个commit创建出来的分支。
在例子中，master^表示master分支的前一个提交。关于commit的表示方法，git有很多表示方法，这里
先不说。我们在commit一节中再详细描述。

set-upstream比较容易理解，就是设置该分支的upstream分支。一旦设置，在push时，无需远程分支
直接之用git push就可以push到需要的分支上。

创建容易，删除也容易，执行如下命令：

    Usage：
    git branch (-d | -D) [-r] <branchname>...
    Example:
    git branch -d new-branch

加上-r 选项可以删除远程的分支，不仅可以删除本地分支，也可以删除远程分支。但是只有在push后，远程分支
会被删除掉。所以小心使用删除功能哦～～～

最后来看看如何对branch进行修改。对branch修改有很多操作，这里我们列出来，不做太多详细分析：

* 修改远程分支: git branch (--set-upstream-to=<upstream> | -u <upstream>) [<branchname>]/
git branch --unset-upstream [<branchname>]  
* 修改分支描述: git branch --edit-description [<branchname>]
* 获取并最新的远程分支信息： git fetch [remote-name]
* 获取远程分支信息并试图合并到本地branch上：git pull [remote-name]
* 从别的分支merge change: git merge [from-branch-name]


Git Commit操作
-----------------------------
Commit是Git网络模式下通信的基本单位，不同的git之间通信就是通过commit作为基本单位来进行
交流的。当我们从upstream 拉取代码时，git会比较哪些commit没有在本地出现，如果没有出现就会
replay这个commit，如果在replay过程中，发现了本地对这个文件也有修改就会产生冲突，用户修改
改冲突后，操作才能成功。所有commit是非常重要的操作。

在开始介绍commit之前先来说一下commit在git里面如何表示的。在创建git的commit的时候，会根据
提交的内容使用SHA1的Hash摘要算法生成如下的代码作为这个commit的id：

    bd9dbf5aae1a3862dd1526723246b20206e5fc37

除了使用这样长的id，你也可以使用前几位，只要在commit list没有冲突你都可以使用，比如：

    git show bd9dbf5aae1a3862dd1526723246b20206e5fc37
    git show bd9d
    git show bd9db

除了这几种方式之外，git里面的reference，指向的也是commit，所以可以把这些reference当作
commit 的别名，比如：

   git show HEAD # HEAD是当前工作目录最新的commit
   git show BRANCH-NAME # BRANCH-NAME 是这个branch最新的commit
   git show <remote-name>/<remote-branch> # 是这个remote branch最新的commit

要看可以使用哪些reference，可以到.git目录里面，里面有：
    ├── FETCH_HEAD
    ├── HEAD
    ├── ORIG_HEAD
    └── refs
        ├── heads
        │   └── master
        ├── remotes
        │   ├── offical
        │   │   ├── master
        │   │   ├── temp
        │   │   ├── test2
        │   │   └── test3
        │   └── teamA
        │       └── master
        └── tags
上面这些都可以用作commit id。因为他们本来就是commit id的别名。

除了使用ref，还可以使用ref log。 顾名思义，ref log 指的是针对reference变动生成的log。
使用git reflog 命令可以查看reference HEAD 的变动：

    git reflog HEAD
    5393e0d HEAD@{0}: reset: moving to HEAD^
    e152094 HEAD@{1}: commit (amend): change some code
    0e735a1 HEAD@{2}: commit (amend): change some code
    cdaa90f HEAD@{3}: commit: message
    5393e0d HEAD@{4}: commit: add myversion
    c48ae4a HEAD@{5}: pull offical: Merge made by the 'recursive' strategy.
    59f18e2 HEAD@{6}: pull offical: Merge made by the 'recursive' strategy.
    1de1710 HEAD@{7}: initial pull

reflog用的不多，所以这里不再特别详述。

另外，其他比较常用表示commit的有：

* HEAD^ HEAD的上一个commit
* HEAD^^ HEAD的上上一个commit，
* 如果不想使用^^^^^^^这样的符号，使用HEAD~7即可。
* 可以使用range来表示多个commit，所以git log master..experiment
意思是所有commit中能到达expriment但不能为到达master提供帮助的commits，如果master和experiment
是在同一个分支上，但不同版本， 可以表示两个分支的差距。使用...则表示两个分支之间的不同。



常见的commit的操作包括：

* 创建一个commit: git commit -m "message"
* 修改最新的commit: git commit --amend， 你可以修改commit的file list和content
* 删除最新的commit: git reset --hard HEAD^
* 恢复到之前的commit：git revert <commit-id> 该操作会生成一个revert的commit
* 合并删除或者编辑多个commit： git rebase -i ORIGIN_HEAD
* 挑一个commit应用到当前分支上： git cherry-pick <commit-id>

注意在本地操作时，你可以方便的修改这些commit，一旦push到远程，你就没有这些能力了。所以在push
之前要确保你的commit是OK的。

Git Stash 操作
-----------------------------
Git还提供了Stash的功能，Stash的意思就是仓库啦。比如你在本地正在做一个事情，突然有一个比较着急的
活需要先干，你又不想把本地的变化提交的commit里， 那你就可以把你的change暂时放在Stash里。
呆本地着急的活改完了，提交了，你可以再把之前的change从Stash里提取出来继续开发。

Git Stash的操作如下：

      ➜  myVersion git:(e152094) git stash
      apply   -- apply the changes recorded in the stash
      branch  -- branch off at the commit at which the stash was originally created
      clear   -- remove all the stashed states
      create  -- create a stash without storing it in the ref namespace
      drop    -- remove a single stashed state from the stash list
      list    -- list the stashes that you currently have
      pop     -- remove and apply a single stashed state from the stash list
      save    -- save your local modifications to a new stash
      show    -- show the changes recorded in the stash as a diff

因为Git stash比较简单，所以这里不再详细介绍。
