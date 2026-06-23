---
layout: default
title: 第17章 GitHub Actions：自动化流水线
---

[← 上一章](chapter16_cli_tools.md) \| [目录](index.md) \| [下一章 →](chapter18_intuition.md)

## 第十七章 GitHub Actions：自动化的流水线

我年轻时在厂里做过工，那时最服的就是流水线：原料进去，成品出来，当中的过程全自动，不用人插手。我那时想，写代码要也能这样就好了——提交代码，测试、构建、部署，一气呵成。

后来撞见了 CI/CD，尤其是 GitHub Actions。这就是写代码的人的流水线。

### 一

什么是 GitHub Actions？

它是 GitHub 给的一套自动化服务。你在仓库里定下几条工作流（Workflow），逢着特定的事（譬如推送代码、开 PR），就自动跑一串活计。

这些活计可以是：跑测试、查代码风格、构建项目、部署到服务器、发通知。

### 二

Claude Code 怎么跟 GitHub Actions 搭手？

头一样，它能替你写工作流文件。这些文件是 YAML 格式，定下什么时候触发、跑什么活、要什么环境。

写这些文件有点磨人，得查文档、记语法。撂给 Claude Code 就省事：

```
帮我写个 GitHub Actions 工作流：每次 Push 时跑测试，
测试过了就构建 Docker 镜像，推到 Docker Hub。
```

它就给你生成一份齐整的工作流文件，你直接提交到 `.github/workflows/` 目录就成。

### 三

第二样，它能替你查 Actions 的岔子。

Actions 有时会挂，报错信息一大片，看不明白。你把日志贴给它：「这个 Actions 挂了，帮我瞅瞅是什么毛病？」它便去读日志，揪出错因，给你修补的法子。这比你自个儿一行行抠日志，省事多了。

### 四

再一样，它能替你把 Actions 调利索。

Actions 的运行时长和资源是要算钱的（虽说有免费的额度）。工作流写得糙，就可能跑得没完，白耗资源。Claude Code 能替你把工作流剖一剖，提点优化的路子：哪些步子能并着跑，哪些缓存能用上，哪些步子能省。

这就像身边有个老到的运维，替你把流水线调顺。

### 五

可 GitHub Actions 也有坑。

有时它跑得好好的，忽地因为某个依赖升了级就挂了；有时同一套配置，本机跑得通，搁 Actions 里就卡壳。

所以用 Actions 要：

- 把依赖的版本钉死，别用 `latest`
- 多试，确保换了环境也跑得通
- 把通知配好，挂了能第一时间知道

总之，GitHub Actions 是当下开发流程里的一件要紧家伙，Claude Code 能让你上手更省力。自动化是效率的命门，值得搭工夫去学。

---

[← 上一章](chapter16_cli_tools.md) \| [目录](index.md) \| [下一章 →](chapter18_intuition.md)
