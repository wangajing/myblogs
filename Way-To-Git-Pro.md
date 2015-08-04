Git专家之路
==========
Git这个在只有几天之内写出来的源代码管理工具，如今发展成了苍天大树，大量的开源项目已经放弃
传统的svn或cvs，转入git的阵营。通过该文档，你可以了解到git的工作原理，还有git的专家级的
使用方法，从而降低

Git工作原理
======================
传统的SCM工作原理
--------------------
使用过svn或者perforce的都知道，你必须要有一个搭建一个服务器，才能让你的代码使用版本控制工具。
一旦开始使用这个服务器，您想切换到另外的服务器就非常麻烦。如果服务器down了， 你的commit就不能
分享给别人，大家只能干等着服务器修好后，你的commit提交了才能基于你的工作继续。 所有传统的版本控制
工具就是典型的客户端－服务器结构。一切操作都要与服务器同步。

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

总之，在单机模式下你可以使用服务器模式下的所有功能。所以我们可以先git当成一个强大的
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

Git Commit操作
-----------------------------
Commit是Git网络模式下通信的基本单位，可以将每个commit当作一个对象。

Git Branch操作
-----------------------------
上一节，我们重点分析了git的工作原理，和如何让git跟多个server一起互动。这一节，我们重点分析
如何进行分支管理，包括创建分支，合并分支，冲突管理，创建Commit



Git Stash 操作
-----------------------------
Git还提供了
