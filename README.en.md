<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Li-Sanze&fontSize=42&fontColor=fff&animation=twinkling&fontAlignY=32&desc=Structural%20Engineer%20for%20AI%20Systems&descSize=16&descAlignY=52" width="100%" />

<p align="center">
  English · <a href="README.md">简体中文</a>
</p>

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=22&pause=1000&color=58A6FF&center=true&vCenter=true&repeat=true&width=680&height=45&lines=Local+optimum+%E2%89%A0+Global+optimum.;AI+produces.+Structure+verifies." alt="Typing SVG" />
</p>

---

## 🧭 What I'm Thinking About

We're in the golden age for learners — people with ideas can now collide with AI, validate fast, and iterate fast. But only if you can trust what it produces. And those outputs aren't just code — they include plans, design decisions, and decision records.

Models tend to agree with their own outputs, and a shared context reinforces that agreement — abandoned approaches, retry artifacts, implicit assumptions, each one whispering "the previous call was right."

**AI is an effective producer, but not a reliable self-verifier.**

### Starting from a Single Diff

When AI coding assistants generate code, they accumulate local assumptions throughout the session. If the review step reuses the same context, the reviewer tends to inherit the author's framing rather than independently re-deriving whether the change is correct.

**CrossReview**'s core insight: **You don't need a different model — just a different context.** It packages changes, intent, and focus areas into a `ReviewPack` and hands it to a fully context-isolated review session — no original conversation, no reasoning trace, no tool history. The protocol itself isn't limited to code; any reviewable structured artifact falls within its conceptual boundary. v0 starts with code diffs.

This is **local optimum**: ensuring each change is independently verified under architectural separation.

### But Local Optimum ≠ Global Optimum

While building [Sopify](https://github.com/sopify-ai/Sopify), I realized a deeper problem: plans, blueprints, decision records — these **machine contracts** produced by AI workflows are **accumulable project assets**, and they need verification too.

You can perfectly verify every diff —  
but passing every review doesn't mean long-term plans are consistent;  
completing every task doesn't mean knowledge assets are trustworthy;  
every agent can produce output, but that doesn't mean the workflow can recover.

**Sopify** makes the AI workflow itself **recoverable, reviewable, and accumulable**. It pauses when facts are missing; waits for confirmation at decision points; resumes from current state after interruption instead of improvising from scratch.

### Structural Engineer

Behind both projects is a unified framework. I call it **structural engineering**:

> **A structural engineer isn't someone who writes better prompts — it's someone who designs boundaries, contracts, state, and feedback loops.** They care not about whether a single answer looks good, but whether an AI system remains stable across multiple turns, tools, and artifacts.

In the context of AI-assisted programming, a structural engineer asks:

- What is the minimal stable unit?
- Which artifacts must be structured?
- Which states must be recoverable?
- Which decisions must wait for confirmation?
- How does local correctness compose into global stability?

| Level | Minimal Stable Unit | Verification Mechanism | Project |
|-------|-------------------|----------------------|---------|
| **Local** | One diff + isolated context | Context-isolated cross-review | [CrossReview](https://github.com/Li-Sanze/cross-review) |
| **Global** | Full workflow lifecycle | Checkpoints, blueprints, traceable decision chains | [Sopify](https://github.com/sopify-ai/Sopify) |

CrossReview ensures the correctness of each unit. Sopify ensures their composition produces a correct, sustainably accumulable whole.

**Local verification is necessary. Global governance is sufficient. Together, they give AI workflows a chance to move from "can generate" to "can be trusted".**

---

## 🔨 Building

<table>
<tr>
<td width="50%" valign="top">

### [CrossReview](https://github.com/Li-Sanze/cross-review)

Context-isolated cross-verification

- 🎯 **Core mechanism**: Input isolation, not model diversity
- 📦 `ReviewPack` → isolated review → structured findings
- 🔭 Not just code — applies to any reviewable artifact
- 🔌 Plugs into any AI coding workflow
- 📍 **Position**: Local optimum — independent verification per change

</td>
<td width="50%" valign="top">

### [Sopify](https://github.com/sopify-ai/Sopify)

Recoverable, reviewable, accumulable AI coding workflow

- 🔄 Resume from current state after interruption
- 📋 Plans, blueprints, history become reusable project assets
- ⏸️ Pauses when facts are missing, waits at decision points
- 🌐 Supports Codex / Claude / Trae CN multi-host
- 📍 **Position**: Global optimum — workflow-level structural governance

</td>
</tr>
</table>

---

## 🛠 Tech Stack

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
