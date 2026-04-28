<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Li-Sanze&fontSize=42&fontColor=fff&animation=twinkling&fontAlignY=32&desc=AI%20%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BB%93%E6%9E%84%E5%B7%A5%E7%A8%8B%E5%B8%88&descSize=16&descAlignY=52" width="100%" />

<p align="center">
  <a href="README.en.md">English</a> · 简体中文
</p>

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&pause=1000&color=58A6FF&center=true&vCenter=true&repeat=true&width=680&height=45&lines=Local+optimum+%E2%89%A0+Global+optimum.;AI+produces.+Structure+verifies." alt="Typing SVG" />
</p>

---

## 🧭 我在想什么

我们正处于学习者的黄金时代——有想法的人可以和 AI 快速碰撞、快速验证、快速迭代。但前提是，你能信任它产出的东西。这些产出不只是代码——还有计划、设计方案、决策记录。

模型倾向于认同自己的产出，共享的上下文不断强化这种认同——被放弃的方案、重试的痕迹、隐含的假设，每一条都在说"之前没错"。

**AI 是高效的生产者，但不是可靠的自我验证者。**

### 从一个 diff 开始

AI 编程助手在生成代码时，会在会话中不断积累局部假设。当复用同一个上下文做审查时，审查者倾向于继承作者的框架，而不是独立地重新推导变更是否正确。

**CrossReview** 的核心洞察：**你不需要换一个模型，只需要换一个上下文。** 把变更、意图、关注点打包成 `ReviewPack`，交给上下文完全隔离的审查会话——不继承任何原始对话、推理轨迹或工具历史。协议本身不限于代码，任何可审查的结构化产物都在概念边界内。v0 从 code diff 开始。

这是**局部最优**：确保每一次变更在架构分离的条件下被独立验证。

### 但局部最优推导不出全局最优

在构建 [Sopify](https://github.com/evidentloop/Sopify) 的过程中，我意识到一个更深的问题：计划、蓝图、决策记录——这些 AI 工作流产出的**机器契约**，是**可沉淀的项目资产**，它们同样需要被验证。

你可以完美验证每一个 diff——  
但每个 diff 都通过审查，不代表长期计划一致；  
每次任务都完成，不代表知识资产可信；  
每个 agent 都能产出，不代表工作流可以恢复。

**Sopify** 在做的事情是：让 AI 工作流本身变得**可恢复、可审查、可积累**。事实缺失时停下来询问；决策分叉时等待确认；中断后从当前状态恢复，而不是从零即兴发挥。

### 结构工程师

这两个项目背后有一个统一的思考框架。我把它叫做**结构工程**：

> **结构工程师不是更会写提示词的人，而是设计边界、契约、状态和反馈回路的人。** 他关心的不是单次回答是否漂亮，而是一个 AI 系统在多轮、多工具、多产物之间是否仍然稳定。

在 AI 辅助编程的语境下，结构工程师要回答的问题是：

- 什么是最小稳定单元？
- 哪些产物必须结构化？
- 哪些状态必须可恢复？
- 哪些决策必须等待确认？
- 局部正确如何组合成全局稳定？

| 层级 | 最小稳定单元 | 验证机制 | 项目 |
|------|-------------|---------|------|
| **局部** | 一次 diff + 隔离上下文 | 上下文隔离的交叉审查 | [CrossReview](https://github.com/Li-Sanze/cross-review) |
| **全局** | 完整的工作流生命周期 | checkpoint、blueprint、可追溯的决策链路 | [Sopify](https://github.com/evidentloop/Sopify) |

CrossReview 确保每个单元的正确性。Sopify 确保单元之间的组合产生正确的、可持续积累的整体。

**局部验证是必要条件，全局治理是充分条件。两者组合起来，AI 工作流才有机会从"会生成"走向"可信任"。**

---

## 🔨 正在构建

<table>
<tr>
<td width="50%" valign="top">

### [CrossReview](https://github.com/Li-Sanze/cross-review)

上下文隔离的交叉验证

- 🎯 **核心机制**：输入隔离，不是模型多样性
- 📦 `ReviewPack` → 隔离审查 → 结构化结论
- 🔭 不限于代码——适用于任意可审查产物
- 🔌 可插入任何 AI 编程工作流
- 📍 **定位**：局部最优——每个变更的独立验证

</td>
<td width="50%" valign="top">

### [Sopify](https://github.com/evidentloop/Sopify)

可恢复、可审查、可积累的 AI 编程工作流

- 🔄 中断后从当前状态恢复，而非从零开始
- 📋 计划、蓝图、历史成为可复用的项目资产
- ⏸️ 事实缺失时暂停，决策点等待确认
- 🌐 支持 Codex / Claude / Trae CN 多宿主
- 📍 **定位**：全局最优——工作流级别的结构治理

</td>
</tr>
</table>

---

## 🛠 技术栈

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Claude](https://img.shields.io/badge/Claude-CC785C?style=flat-square&logo=anthropic&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Shell](https://img.shields.io/badge/Shell-4EAA25?style=flat-square&logo=gnubash&logoColor=white)

---

<p align="center">
  <img src="https://komarev.com/ghpvc/?username=Li-Sanze&color=58A6FF&style=flat-square&label=Profile+Views" />
</p>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer" width="100%" />
