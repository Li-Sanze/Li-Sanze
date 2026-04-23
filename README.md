<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Li-Sanze&fontSize=42&fontColor=fff&animation=twinkling&fontAlignY=32&desc=AI%20%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BB%93%E6%9E%84%E5%B7%A5%E7%A8%8B%E5%B8%88&descSize=16&descAlignY=52" width="100%" />

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&pause=1000&color=58A6FF&center=true&vCenter=true&repeat=true&width=680&height=45&lines=Understand+%C2%B7+Use+%C2%B7+Make+the+rules.;Local+optimum+%E2%89%A0+Global+optimum.;Same+model%2C+clean+session%2C+real+findings." alt="Typing SVG" />
</p>

---

## 🧭 我在想什么

我们正处于学习者的黄金时代。AI 把数年的试错压缩到了数周——但前提是，你能信任它产出的东西。

**AI 助手是强大的生产者，却是糟糕的自我验证者。**

这不是提示词的问题。它是架构问题。

### 从一个 diff 开始

每个 AI 编程助手（Claude、Copilot、Cursor……）在生成代码时，都会在会话中积累大量的局部假设：被放弃的方案、重试的痕迹、工具调用的副产物。当审查步骤复用同一个上下文时，审查者倾向于继承作者的框架，而不是独立地重新推导变更是否正确。

**CrossReview** 的核心洞察很简单：**你不需要换一个模型，你只需要换一个上下文。** 相同的模型，全新的会话，真实的发现。它把 diff、意图声明、关注点打包成一个 `ReviewPack`，交给一个上下文完全隔离的审查会话。审查者不继承任何原始对话、推理轨迹或工具历史——它只从审查产物本身出发进行验证。

这是**局部最优**：确保每一次代码变更在架构分离的条件下被独立验证。

### 但局部最优推导不出全局最优

在构建 [Sopify](https://github.com/sopify-ai/Sopify) 的过程中，我意识到一个更深的问题：

AI 工作流产出的不只是代码。它还会产出计划（plan）、蓝图（blueprint）、决策记录（history）——这些是**机器契约**，是**可沉淀的项目资产**。它们同样是 AI 生成的产物，同样需要被验证。

你可以完美验证每一个 diff。但如果整体工作流的知识积累是断裂的、决策链路是不可追溯的、中断后的恢复是靠即兴发挥的——系统仍然会失败。这就是局部最优无法推导出全局最优的根本原因。

**Sopify** 在做的事情是：让 AI 工作流本身变得**可恢复、可审查、可积累**。当事实缺失时，它停下来询问；当路径需要决策时，它等待确认；当工作中断时，它从当前状态恢复，而不是从零开始即兴发挥。计划、蓝图、历史记录不再是一次性的聊天记录，而是可复用的项目资产。

### 结构工程师

这两个项目背后有一个统一的思考框架。我把它叫做**结构工程**：

> **结构工程师的核心工作是：维护最小结构稳定单元，并将其组合推导到全局最优。**

在 AI 辅助编程的语境下：

| 层级 | 最小稳定单元 | 验证机制 | 项目 |
|------|-------------|---------|------|
| **局部** | 一次 diff + 隔离上下文 | 上下文隔离的交叉审查 | [CrossReview](https://github.com/Li-Sanze/cross-review) |
| **全局** | 完整的工作流生命周期 | checkpoint、blueprint、可追溯的决策链路 | [Sopify](https://github.com/sopify-ai/Sopify) |

CrossReview 确保每个单元的正确性。Sopify 确保单元之间的组合产生正确的、可持续积累的整体。

**局部验证是必要条件，全局治理是充分条件。两者缺一不可。**

---

## 🔨 正在构建

<table>
<tr>
<td width="50%" valign="top">

### [CrossReview](https://github.com/Li-Sanze/cross-review)

上下文隔离的 AI 代码交叉验证

- 🎯 **核心机制**：输入隔离，不是模型多样性
- 📦 `ReviewPack` → 隔离审查 → 确定性裁决
- 📊 初步评估：精确率 1.00，召回率 0.75
- 🔌 可插入任何 AI 编程工作流
- 📍 **定位**：局部最优——每个 diff 的独立验证

</td>
<td width="50%" valign="top">

### [Sopify](https://github.com/sopify-ai/Sopify)

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
