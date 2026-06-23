# 玩转通用智能体之Claude Code

## 第二十七章 Agent SDK：打造你自己的怪兽

到这儿，你已经会使唤这只怪兽了。但总有那么一天，你会冒出个念头：**我能不能自己养一只？**——不在终端里聊天，而是把它当成一个库，嵌进我自己的程序里，让它替我的应用自动干活。

这就是 **Agent SDK** 干的事。

### 一

它给你的，是**驱动 Claude Code 的那同一套东西**：同样的工具、同样的 agent 循环、同样的上下文管理——只不过现在你用 Python 或 TypeScript 来编程调用。

最小的例子，短得让人感动：

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions

async def main():
    async for message in query(
        prompt="Find and fix the bug in auth.py",
        options=ClaudeAgentOptions(allowed_tools=["Read", "Edit", "Bash"]),
    ):
        print(message)  # 它自己读文件、找 bug、改掉

asyncio.run(main())
```

TypeScript 版几乎一模一样：

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "Find and fix the bug in auth.ts",
  options: { allowedTools: ["Read", "Edit", "Bash"] }
})) {
  console.log(message);
}
```

读文件、跑命令、改代码这些工具都是**内置**的，你不用自己实现工具怎么执行——这正是它和底层 API 客户端最大的区别。

### 二

装它：

```bash
npm install @anthropic-ai/claude-agent-sdk    # TypeScript
pip install claude-agent-sdk                  # Python，需 3.10+
```

然后去 Console 拿个 API key，搁进环境变量：

```bash
export ANTHROPIC_API_KEY=your-api-key
```

要提醒一句：Agent SDK 走的是 **API key**，不是 claude.ai 的订阅登录。你自己拿它做的产品，也不能给终端用户套上 claude.ai 的登录和额度——这是规矩。

### 三

让 Claude Code 强大的那些花活儿，SDK 里一样不少：

- **内置工具**：Read / Write / Edit / Bash / Glob / Grep / WebSearch / WebFetch……开箱即用。
- **Hooks**：在 agent 生命周期的关键节点跑你的回调，记日志、拦截、改写它的行为。
- **Subagents**：派专门的子 agent 干专门的活，主 agent 调度、汇总。把 `Agent` 加进 `allowedTools` 就自动放行。
- **MCP**：接数据库、浏览器、各种 API——和你在终端里用的是同一套 MCP。
- **权限**：精确控制它能用哪些工具。只给 `Read`/`Glob`/`Grep`，就是一只只看不动手的「只读怪兽」。
- **会话**：抓住 session id 就能续上、能 fork 出去试不同走法。

### 四

那么问题来了：什么时候用 SDK，什么时候用命令行？

官方的账算得很清楚——交互式开发、一次性的活儿，用 **CLI**；CI/CD 流水线、嵌进自己应用、上生产的自动化，用 **SDK**。很多团队两个都用：白天日常开发使 CLI，生产自动化挂 SDK。手法是相通的，平移过去就行。

还有一档叫 **Managed Agents**，是 Anthropic 替你托管 agent 和沙箱的 REST 服务；而 Agent SDK 是个**跑在你自己进程里**的库，活儿干在你自己的机器和服务上。常见的路子是：先用 SDK 在本地把原型捏出来，上生产再考虑搬到 Managed Agents。

### 五

我养过一条狗。一开始什么都得我教：坐下、握手、别上沙发。教会了之后，它就成了「我的狗」——带着我的规矩、我的脾气。

Agent SDK 给你的就是这么个机会：不再是借用别人那只通用的怪兽，而是按你自己应用的需要，从同一套骨血里，捏出一只**你的**。它读什么、能动什么、在哪个环节停下来问一句——全由你定。

养怪兽这件事，到这一章，算是从「会用」，迈到了「会造」。这一步迈过去，你看 Claude Code 的眼光就不一样了：它不再只是个工具，而成了一块你能往上盖房子的地基。

---
