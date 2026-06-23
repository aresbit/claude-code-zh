# 玩转通用智能体之Claude Code

## 第十三章 Git Worktree：并行世界的章法

我年轻时有个坏毛病：手头同时开一堆项目。一个写到半截，忽地冒出个新念头，扭头又扑到另一个上。结果桌面上窗口堆成一片，乱成一锅粥，到末了连自己做到哪儿都忘了。

后来学了 Git Worktree，才好了大半。这物件让我能在同一个仓库里同时支起好几个工作目录，一个目录一条分支，各干各的，互不搅扰。

Claude Code 也认 Git Worktree，且配合得挺默契。

### 一

什么是 Git Worktree？

说白了，就是在一个 Git 仓库里支起好几个工作目录。一个目录对一条分支，你能在不同目录里同时料理不同的活，省了来回切换的工夫。

譬如你在 `main` 上有个项目，眼下要做个新功能，得开一条 `feature-x` 分支。照常的路子，你 `git checkout -b feature-x`，然后在这条分支上干；想回 `main` 瞧一眼，又得切回去。

用 Worktree，你能单给 `feature-x` 支一个目录，两个目录一并存着，一并使着。

### 二

怎么支一个 Worktree？

```bash
git worktree add ../project-feature-x feature-x
```

这会在上一级目录里支起一个 `project-feature-x` 文件夹，里头就是 `feature-x` 分支的家当。

你尽可以在原来的目录里接着料理 `main` 的事，同时在 `project-feature-x` 里料理新功能，两头互不碍着。

### 三

Claude Code 怎么跟 Worktree 配合？

你能在不同的 Worktree 里各起一个 Claude Code 会话。每个会话只盯着当下这个 Worktree 的家当，上下文窗口不会让别的分支的东西搅浑。

这在同时开几个功能的时候顶顶有用。你能：

- 在一个 Worktree 里做功能甲，让一个会话搭把手
- 在另一个 Worktree 里做功能乙，让另一个会话搭把手
- 两头一并推进，互不搅扰

这就像有两间独立的办公室，一间办一桩事，不会串台。

### 四

Worktree 还有个好处：便于比对。

有时你要比两条分支的出入，或者把一条分支的改动挪到另一条上。手里有两个 Worktree，便能一并打开，差别看得真真切切。Claude Code 也能替你做这种跨 Worktree 的剖析，譬如「瞧瞧 `feature-x` 比 `main` 改动了哪些地方」。

### 五

可 Worktree 也有它的限。

头一桩是占地方。每个 Worktree 都是一个齐整的工作目录，文件一多，磁盘就吃紧。

第二桩是容易乱。Worktree 一多，你兴许就闹不清自己身在哪一个里头——尤其当你在好几个 Worktree 里都起了 Claude Code，得当心别把命令发错了门。

所以 Worktree 配两三摊活正合适，再多反倒乱。用完记得收摊：

```bash
git worktree remove ../project-feature-x
```

说到底，Git Worktree 是并行开活的好家什，配上 Claude Code，能让你一手料理几摊事而不慌。使得在理，收拾得干净，才掂得出它的好。

---
