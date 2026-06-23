# 玩转通用智能体之Claude Code

## 第十三章 Git Worktree：并行世界的艺术

我年轻时有个坏习惯，就是同时开很多个项目。一个项目写到一半，突然有了新想法，就切换到另一个项目。结果桌面上一堆窗口，乱七八糟，最后连自己做到哪都忘了。

后来学了Git Worktree，情况好了很多。这个功能让我可以在同一个仓库里同时开多个工作目录，每个目录是一个分支，互不干扰。

Claude Code也支持Git Worktree，而且配合得挺好。

### 一

什么是Git Worktree？

简单说，就是你可以在一个Git仓库里创建多个工作目录。每个工作目录对应一个分支，你可以在不同的目录里同时处理不同的任务，不用来回切换。

比如你在`main`分支上有个项目，现在要做一个新功能，需要创建`feature-x`分支。正常情况下，你得`git checkout -b feature-x`，然后在这个分支上工作。如果你想回到`main`分支看看什么，又得切回去。

用Worktree，你可以创建一个单独的目录给`feature-x`分支，两个目录同时存在，同时可用。

### 二

怎么创建Worktree？

```bash
git worktree add ../project-feature-x feature-x
```

这会在上级目录创建一个`project-feature-x`文件夹，里面就是`feature-x`分支的内容。

你可以在原来的目录里继续处理`main`分支的事情，同时在`project-feature-x`里处理新功能，两边互不干扰。

### 三

Claude Code怎么配合Worktree？

你可以在不同的Worktree里启动不同的Claude Code会话。每个会话只关注当前Worktree的内容，context window不会因为其他分支的内容而被污染。

这在并行开发多个功能的时候特别有用。你可以：
- 在一个Worktree里做功能A，让Claude Code协助
- 在另一个Worktree里做功能B，让另一个Claude Code会话协助
- 两边同时进行，互不干扰

这就像是有两个独立的办公室，每个办公室处理一个项目，不会串台。

### 四

Worktree还有个好处，就是方便比较。

有时候你需要对比两个分支的差异，或者把一个分支的改动应用到另一个分支。如果有两个Worktree，你可以同时打开它们，直观地看到区别。

Claude Code可以帮你做这种跨Worktree的分析，比如"看看feature-x分支相对于main改了哪些地方"。

### 五

但Worktree也有限制。

首先是磁盘空间。每个Worktree都是一个完整的工作目录，文件多的话会占用不少空间。

其次是复杂性。Worktree多了，你可能会搞混自己在哪个Worktree里。特别是如果你同时在多个Worktree里启动了Claude Code，得小心别把命令发错地方。

所以Worktree适合并行处理2-3个任务，太多了反而乱。用完记得清理：

```bash
git worktree remove ../project-feature-x
```

总之，Git Worktree是并行开发的好工具，配合Claude Code使用，能让你更高效地处理多个任务。合理使用，保持整洁，才能发挥它的优势。

---