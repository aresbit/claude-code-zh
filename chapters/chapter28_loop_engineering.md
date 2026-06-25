# 玩转通用智能体之 Claude Code

## 第二十八章 Loop 工程：让循环替你干活

Peter Steinberger 最近有句话在圈里传得挺广：

> “你不该再直接给编程 agent 写提示了。你应该设计一些循环，让循环去提示你的 agent。”

Boris Cherny 也说得更直白：

> “我已经不直接提示 Claude 了。我有一些循环在跑，它们会自己提示 Claude、自己决定下一步。我的工作，是写这些循环。”

这两句话指向的其实是同一件事：**Loop Engineering**——不是写更好的提示词，而是设计一套能自己运转、自己找活、自己验收、自己决定下一步的系统。

这一章就来聊聊这到底是怎么回事，以及它在 Claude Code 里长什么样。

---

### 一、从“一句一句聊”到“设计一个循环”

前两年用 AI 写代码，基本模式是这样的：

你写一句提示，agent 回一段代码；你读一遍，再写一句提示，让它改；它再回，你再读……循环往复，但**驱动力是你**。你握着工具，一次一次地抡。

Loop engineering 把这个模式翻过来了：

你不再一句一句地跟 agent 聊天，而是**一次性设计好一个循环系统**——它自己发现任务、自己分派给 agent、自己检查结果、自己把结果记下来，然后再决定下一轮要做什么。你做的事情从“持续握方向盘”变成“设计好路线和检查点”。

这跟前面聊过的 agent harness（给单个 agent 搭一个能跑的环境）还不太一样。Loop engineering 比 harness 再高一层：harness 是一辆能跑的车，loop engineering 是让它按时刻表自己发车、自己装卸货、自己回来的整套调度系统。

---

### 二、循环的六块积木

Addy Osmani 在文章里把 loop 拆成了五块积木，再加上一块“记忆”。这五加一在 Claude Code 里几乎都能找到对应物。

| 积木 | 在循环里干嘛 | 在 Claude Code 里长什么样 |
|------|-------------|------------------------|
| **Automations（自动化）** | 按周期自己触发，做发现和分拣 | `/loop`、`/goal`、hooks、cron、GitHub Actions |
| **Worktrees（工作树）** | 让多个 agent 并行干活而不互相踩 | `git worktree`、`--worktree`、subagent 的 `isolation: worktree` |
| **Skills（技能）** | 把项目知识固化下来，不再每次重新解释 | `.claude/skills/<技能名>/SKILL.md` |
| **Plugins / Connectors（插件/连接器）** | 让循环能碰你的真实工具：issue 跟踪、CI、Slack | MCP servers、plugins |
| **Sub-agents（子代理）** | 一个人写，另一个人审，避免“自己给自己打分” | `.claude/agents/`、agent teams |
| **State / Memory（状态/记忆）** | 跨轮次记住“干了什么、还差什么” | `AGENTS.md`、progress 文件、Linear 等 |

这五块里，**Automations 是心跳**。没有它，循环就不是循环，只是你手动跑了一次脚本。`/loop` 让某个提示按固定节奏重跑；`/goal` 更厉害一点，它让 agent 一直干，直到你写的终止条件被满足。比如：

```
/goal 让 test/auth 下所有测试通过，并且 lint 没有报错
```

`/goal` 的一个关键细节是：每轮结束后，会用另一个“新鲜的模型”来判断条件是否满足。也就是说，**干活的 agent 和验收的 agent 不是同一个**。这和 sub-agent 里“maker/checker split”的思路一模一样，只是用到了循环的停止条件上。

---

### 三、Skills、Worktrees 和 Sub-agents 为什么缺一不可

如果只有 `/goal`，循环最多只能算个“会重复跑提示的脚本”。真正让它成形的，是另外三块积木。

**Skills 解决的是“ intent debt（意图债务）”**。每次 Claude Code 新开个会话，它都像是第一次来这个项目，会拿自信去填你意图里的空洞。Skills 把这些意图一次性写进 `SKILL.md`：代码风格怎么定、构建命令是什么、哪些坑别踩、上次那次事故是因为什么。循环每转一圈，都能读到这份外部记忆，不用再从零推导。

**Worktrees 解决的是并行冲突**。一旦你开始同时跑多个 sub-agent，文件冲突就是第一个落地的麻烦。两个 agent 改同一个文件，跟两个工程师没沟通就改同一处代码没区别。`git worktree` 给每个 agent 一个独立分支和目录，物理上互相隔离，跑完再合并。

**Sub-agents 解决的是“自己给自己打分”**。写代码的模型天然会对自己的代码过于宽容。让另一个模型、带着不同的系统提示去审查，能抓住很多“创造者”选择性忽略的问题。在循环里这一点尤其重要：因为循环很可能是无人值守地跑，**一个你不信任的 verifier，就不配让你离开电脑**。

---

### 四、一个典型循环长什么样

把上面这些拼起来，一个 loop 可能是这样的：

每天早上 9 点，一个 automation 启动。它的提示调用了一个 triage skill，这个 skill 会读取昨天的 CI 失败、打开的 issue、最近的 commit，然后把值得处理的事情写进一个 markdown 状态文件里。

对状态文件里的每一条，循环开一个独立的 worktree，派一个“实现者” sub-agent 进去写修复；同时派一个“审查者” sub-agent，对照项目 skill 和现有测试去检查修复。

如果修复通过审查，MCP connector 自动开 PR、更新 Linear ticket；如果搞不定，就放到 triage inbox 里等人来看。

第二天循环再启动时，先读昨天的状态文件，接着干没干完的活。

你设计好这套东西之后，**再也不用逐条提示它该干什么**。循环自己知道该干什么。

---

### 五、Loop 解决不了的，反而更危险

Loop 不是把工程师删掉，而是把工程师的工作往上挪了一层。下面这三件事，循环不但没解决，甚至可能让问题更严重。

**第一，验收仍然是你负责。**

一个无人值守的循环，也会无人值守地犯错。verifier sub-agent 只能降低概率，不能替你担保“这个 PR 真的能合并”。最终按下合并按钮的人，还是得理解代码。

**第二，理解债务会加速积累。**

循环产出得越快，你越可能懒得看。仓库里的代码在增长，你对代码的理解却可能没跟上。作者管这叫 **comprehension debt**。一个跑得顺的循环，会让这个债务滚得更快。

**第三，最容易的姿势也最危险。**

当循环自己能跑通时，你会很容易产生一种“它说的应该对吧”的惰性。作者把这叫 **cognitive surrender**——用循环来逃避理解工作，而不是加速已经理解的工作。

同一个 loop，两种用法，结果完全相反。差别不在 loop，而在用它的人。

---

### 六、怎么开始

不用一上来就搭一个全自动的每日循环。可以从最小的一步开始：

1. 找一个重复出现的小任务，比如“让某个模块的测试通过且 lint 干净”。
2. 用 `/goal` 把它写成 run-until-done 的形式。
3. 把项目约定写成 Skill，减少每次重新解释的消耗。
4. 如果需要并行，加 worktree 隔离。
5. 如果涉及代码质量，加一个审查 sub-agent。
6. 每次跑完之后，自己一定 review 一遍产出。

Loop engineering 的难点不是让循环跑起来，而是让它在出错时也能被你理解和修正。换句话说：

> Build the loop. But build it like someone who intends to stay the engineer, not just the person who presses go.

---

### 七、小结

Loop engineering 把杠杆点从“一句好提示”移到了“一套好系统”。它需要的不是更好的 prompt 技巧，而是对任务、工具、状态、验收和并行的系统设计能力。

在 Claude Code 里，/loop、/goal、Skills、Worktrees、Sub-agents、MCP 这些功能刚好拼出了这套系统所需的全部零件。把它们组合好，你就能让怪兽不仅听你指挥，还能在你不在的时候继续干活。

但记住：**循环替你跑，不替你负责。**

---
