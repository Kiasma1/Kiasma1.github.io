# 大白话解释 Git 和 GitHub


大白话解释 Git 和 GitHub

<!--more-->

本文来自[大白话解释 Git 和 GitHub](http://blog.jobbole.com/111187/)
![img](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github1.gif)

- [Treehouse – 写给设计师的 Git 入门介绍](http://blog.teamtreehouse.com/git-for-designers-part-1)
- [Roger Dudler – Git 简易教程](http://rogerdudler.github.io/git-guide/)
- [Pluralsight – Github：初学者指南](https://www.pluralsight.com/blog/software-development/github-tutorial)


## 开始Git

**Git 是一种专为处理文本文件而设计的版本控制系统。**因为，归根到底，这就是代码的本质：一堆堆以某种方式联合在一起的文本文件。
Git 是一个可安装应用，它允许你对你自己所做的更改进行注释，用以创建易于导航的系统历史。
**那么， Git 做了什么，是简单地保存文件所做不到的呢？**
从根本上讲，文件保存就是一个简化的版本控制系统，但坦率地说，它并不是一个好用的系统，因为它只能前进。当然，你也许会争论“撤消”按钮可以让你的文件回滚到以前的状态。但我们都清楚，“撤消”按钮有其局限性，最明显示的是，在关闭文件时，文件的过去也随之丢失。

另外，文件保存是非常个人化的。它不能够显示整个系统的历史，只能够显示该文件的。

**Git 是一个软件，它允许你通过提交对一个系统（或一组）文件的历史进行注释。这些提交便是在给定时间点对系统做出的差异“快照”。**

## Github
到目前为止，一切都还不错。但是，**如果 Sally 同时用到两台电脑工作，将会发生什么呢？**问得好。这时，就该用到 Github了。注意，不要和 Git 混淆了。
**Github 获取 Git 中的提交历史，并将其存储在互联网上，因此你可以从任一一台电脑访问它。**你在本机（例如：你当前正在使用的电脑）**推送（pushing）**提交到 Github，然后，从另一台新的或不同的电脑上**拉取（pulling）**这些提交。
![Github1](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github1.jpg)
让我们假设上图为 Sally 的工作流程。她在家里的台式电脑（左边，橘黄色的）上开启她一天的工作。接下来，她完成了几个章节的写作，又做了一些编辑工作，等等。整个过程中，她对工作总共进行了三次策略性的“快照”（Git 提交）。

下午，Sally 常常喜欢带着她的笔记本电脑（上图中的右侧，蓝色的）去咖啡馆写作。今天也不例外。因此，在关闭家里的台式电脑之前，她需要确认当前的Git 提交历史已**推送（push）**到了在线Github。一旦被上传到 Github，这些提交记录就被存储在**远程仓库（remote repository）**中。

我们先来分析一下几个计算机术语：**远程（remote）**仅仅意味着联网（与“本地”的意思相反，和之前我们理解到的意思一样的，代表当前正在使用的电脑）。而**仓库（repository，经常简写为“repo”）**，就是一个具备 Git 超级权限的文件夹。

因此，**Github 就是让你把工作（通过Git提交进行注解）存储在了一个指定的在线文件夹（repo）**。明白了吧？简单。

午餐之后，在当地的一家咖啡馆中，Sally 拿出了她的笔记本电脑。很明显，她想接着家里的工作进度继续。因些，她从 Github 仓库上获取到最新进度的工作。“从 Github 上获取她的工作”，这一过程就叫**拉取（pulling）**。再看一下上面这幅图片，你将看到 Sally 拉取了之前她在家时进行的三个提交。

现在，在她的笔记本电脑上，Sally 有整个系统（包含她的幻想系列的所有文本文件）的最新的完整副本，并能够基于上次的进度，继续工作。她写了更多的章节，对工作进行了两次以上的策略“快照”（提交）。最后，Sally 把这些提交**推送（push）**到 Github 上，结束了这一天的工作。这样第二天上午的时候，在家里的台式电脑上就可以取得这些最新进度的工作。

## 协同工作



**工程团队要如何确保他们的工作不会重叠？**



简而言之，创建分支。将你的 Git 提交历史想像成一棵树。树的主干就是我们谈到的主分支。为了让团队成员避免彼此牵扯，他们在独立于他人的隔离区（在一个功能分支）进行工作，然而最终，每个人的工作成果都会被提交到主代码库 （主分支）。



现在，回到 Sally 的例子。她加入了奇幻作家协会，在这里每个人都与他人合作完成这本书——《奇幻系列生物辞典》。这本辞典更像一本教材，由多个作者共同完成：Sally、Tom 和 Adam。

让我们来看看《奇幻系列生物辞典》项目的在线 Github 仓库，现在的情况是：

![img](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github2.jpg)

如上图所示，树的类比完全适用于奇幻作家协会在这个项目上的合作情况，仓库历史沿主分支向上移动。常规工作流始于每个作者为完成一个工作任务（例如编写章节内容，或排版章节）而在主分支上创建分支。只有当更改得到其他合作作家的批准时，分支才会被合并到主分支上（请谨记，主分支上的内容，才是最终要发布的内容）。

当一个分支的内容**合并（merged）**到主分支时，意味着该分支的内容会覆盖主分支上的。因此，现有内容的任何更改都将会替代之前的。当然，任何新添加的内容也会添加到主分支。实际上，当分支合并到主分支时，该分支的提交历史被添加到主分支提交历史的顶部。

然而，你可能正在思考：**人们在本机的工作和之后才推送到 Github 的工作变更是如何连接到一起的呢？**

关于这个问题，重点在于：你在 Github 的远程仓库是你本机工作项目的一个镜像。这意味着，你在自己的电脑里存储了该项目（例如：一个已设置可进行 Git 提交的文件夹）的本地 Git 仓库。在这个本地的 Git 仓库（再次，这是一个特定术语，指你的电脑里某个启用了 Git 功能的文件夹）中，你拥有与该项目相关的所有文件，在本文的例子中，即《奇幻系列生物辞典》。

它的工作原理很像 Dropbox ：你在不同的设备（你的家庭电脑、办公室电脑，等等）上创建本地文件夹，进行工作并更新这些文件。最后，这些操作被同步到网络上。然而，我们知道，Git/Github 工作流还包含了一些额外的步骤。首先，你必须有意识地对某一时刻的工作执行“快照”（即执行一次提交）。然后，你必须特意地推送这些提交（push） 到 Github。只有这样，你的工作才被同步到网络上的位置（Github 版本库）。

既然如此，为什么不自动化该工作流呢？为什么不让它像 Dropbox一样，当你更新本地文件时，同时自动更新 Github 上的文件？有很多理由让我们不这么做。最主要的理由是——bugs 。同出版界一样，软件工程中也不是所有写过的东西都要保留。有时，你希望实验一下你的想法，如果实验失败，你希望有一种简单的方式能让工作快速回滚到之前的正确状态上。这也是为什么我们提倡这个经验法则，即在你试图用不同的方法编辑或实验之前，先对当前你希望保留的修改进行提交。频繁地提交小块工作有益无害，事实上，许多工程师为自己能做到这一点而感到自豪。

现在，回到《奇幻系列生物辞典》。由于  Sally 对兽族有较深的了解，她被挑选为写兽族章节。但她不想在没有经过其它合作人员允许的情况下去修改这本书，于是，她创建了一个本地分支，并在该分支上进行写作和提交。然后，**她将本地分支推送到 Github**。像往常一样，Github 的远程仓库是本地库的一个镜像，最新进展显示 Sally 已创建了一个包含部分提交的分支（如下图所示）。

![img](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github3.jpg)

随着她对本章节的持续写作，Sally 进行了更多的提交，并将它们推送到 Github 的在线镜像分支。终于，她准备请 Tom 和 Adam 一起对她的工作进行评审。因此，她在 Github 上发布了一个 **Pull Request（发布请求）**，这是一个 Github 功能，允许她解释该分支相对于主分支做了哪些修改。Github 还提供了一个简易平台，合作人员可以在该平台上针对分支的修改内容进行讨论，并要求 Sally 在分支合并到主分支之前对一些有异议的内容进行修改。

![img](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github4.jpg)

在对部分内容请求修改后，如上图所示，Tom 和 Adam 对 Sally 的分支内容很满意，并决定将她的工作成果合并到 Github 的主分支上。此时，他们所要做的就是将 Sally 之前独立提交的内容，添加到主分支的提交历史顶部：

![img](https://raw.githubusercontent.com/Kiasma1/Kiasma1.github.io/master/posts/2018/Githubpage/Github5.jpg)

此时，Sally可以切换（或**“check out”**）到本地计算机上的主分支，并将先前在功能分支（兽人章节分支）中的独立提交拉取下来。现在她又要在新的主分支上重新开始了：以该主分支为基础为她的下一步工作创建一个新的本地分支，帮助汤姆编辑有关妖精的章节。因此，这一过程又将重复:

1. 创建本地分支
2. 在本地分支上编辑修改，然后提交
3. 推送提交（Push）到 Github
4.  创建发布请求（Pull Request），说明该分支包含了哪些更改
5. 合并（Merge）分支内容到主分支
6. 将主分支上的最新提交拉取（pull）到本地
7. 重复上述步骤

正如你所看到的，这是一个非常流畅的工作流，完美地结合了独立工作与团队协作。你本机的 Git 提供了一个绝妙的方法，即通过由你自己控制和策划的丰富的历史提交，来创建你工作的各种版本。Github 是一个非常棒的在线版本控制工具，不仅存储和提供了清晰的可视化历史记录，而且还能进行协同工作和质量控制。

总而言之，我希望我已经说服你去**尝试使用 Git  和 Github 进行任何项目**。没有理由只有工程师能从这个很棒的工具中受益。毕竟，我们也想看到更多有关兽人的故事。
