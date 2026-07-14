# Li-Sanze

简体中文 · [English](./README.en.md)

我在做 AI 协作开发工具，主要维护 [Sopify](https://github.com/evidentloop/sopify) 和 [EvidentLoop](https://github.com/evidentloop/evidentloop)，也参与社区共建的 [Fireworks Tech Graph](https://github.com/yizhiyanhua-ai/fireworks-tech-graph)。

我平时主要解决三个问题：工作中断后怎么接着做，AI 产物怎么独立审查，审查结果怎么留在计划里。

![我如何构建和审查 AI 辅助产物](./assets/project-loop.svg)

## 主要维护

### [Sopify](https://github.com/evidentloop/sopify)

Sopify 是一套安装到现有 AI 编程工具中的工作流规则，不需要新的编辑器或独立应用。计划、决策、交接和验证记录留在仓库里，换一个 AI 工具或隔一段时间回来仍能继续。

### [EvidentLoop](https://github.com/evidentloop/evidentloop)

从本地 Git diff 开始做独立审查，生成经过校验的 `audit.json` 和可直接打开的 `audit.html`。报告可以记录用户反馈，也可以作为 Sopify 计划里的审计工件。

## 参与的社区项目

### [Fireworks Tech Graph](https://github.com/yizhiyanhua-ai/fireworks-tech-graph)

把自然语言转成可编辑的 SVG 和 PNG 技术图。上面的工作流图也用它完成。

## 其他项目

这些小工具的日常开发也使用 Sopify 和 EvidentLoop：

- [doc-to-sketch](https://github.com/statefulai/doc-to-sketch) — 把文档转成手绘风格配图。
- [tech-report](https://github.com/statefulai/tech-report) — 从代码、commit 和文档整理技术报告。
- [ai-daily-brief](https://github.com/statefulai/ai-daily-brief) — 汇总每日 AI 资讯。
