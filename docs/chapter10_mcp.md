---
layout: default
title: 第10章 MCP：连接世界的桥梁
---

[← 上一章](chapter09_subagents.md) \| [目录](index.md) \| [下一章 →](chapter11_skills.md)

## 第十章 MCP：连接世界的桥梁

我年轻时有个梦想，就是能有一个万能遥控器，控制家里所有的电器。电视、空调、音响、电灯，一个遥控器搞定。后来智能家居真的实现了，我又觉得不够，我想让这个遥控器能控制更多东西：我的车、我的办公室、甚至街上的红绿灯。

MCP（Model Context Protocol）差不多就是Claude Code的"万能遥控器"。

### 一

MCP是什么？

它是Claude Code与外部世界通信的协议。通过MCP，你可以让Claude Code连接各种外部服务：数据库、API、文件系统、消息队列，等等。

没有MCP之前，Claude Code只能操作你本地的文件和命令。有了MCP，它可以与整个世界交互。你可以让它查询数据库、调用第三方API、读写远程文件，甚至控制其他软件。

这就像是从一个封闭的屋子走出去，进入了广阔的世界。

### 二

怎么配置MCP？

在项目根目录或者CLAUDE.md里，你可以定义MCP连接。比如：

```markdown
## MCP Connections

### PostgreSQL Database
- Type: postgres
- Host: localhost:5432
- Database: myapp
- Permissions: read-only

### External API
- Type: http
- Endpoint: https://api.example.com
- Auth: Bearer token
```

配置好之后，Claude Code就能使用这些连接了。你可以直接跟它说："查询一下数据库里用户表的结构"，或者"调用API获取今天的天气数据"。

### 三

MCP极大地扩展了Claude Code的能力。

以前你要查数据库，得自己打开数据库客户端，写SQL语句，看结果。现在你只需要跟Claude Code说句话，它就帮你做了，还把结果整理得漂漂亮亮的。

以前你要调API，得写代码、跑脚本、处理返回结果。现在Claude Code可以直接帮你调试API，分析返回的数据，甚至帮你写调用代码。

这就像是有个全能助手，不仅能帮你写代码，还能帮你操作各种工具，省了你不少事。

### 四

但MCP也有安全问题。

你给了Claude Code访问数据库的权限，它就能读数据库。如果配置不当，它甚至可能修改或删除数据。你给了它API的访问权限，它就能调用那个API。如果API是扣费的，用多了可能会产生费用。

所以配置MCP的时候要小心：
- 只给必要的权限，能read-only就不要给write
- 敏感操作要加确认步骤，不要全自动
- 定期检查MCP的使用情况，看看有没有异常

这就像是你把家门钥匙给了保姆，得相信她，但也得留个心眼。

### 五

总之，MCP是Claude Code的一个强大功能，它让你的AI助手不再局限于本地代码，而是能与整个世界交互。用好了，它能帮你做很多事情；用不好，也可能带来风险。谨慎配置，合理使用，才能发挥它的价值。

---

[← 上一章](chapter09_subagents.md) \| [目录](index.md) \| [下一章 →](chapter11_skills.md)
