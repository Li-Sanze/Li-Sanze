# Sopify × CrossReview 生态总纲 — 背景与现状

> 生成日期：2026-04-26
> 范围：Sopify 核心工作流 + CrossReview 独立验证 + 知识工程底座 + 生态组件

---

## 1. 核心命题

**AI 编码的质量闭环** — Sopify 保障过程可靠，CrossReview 保障产出可验证，两者共同形成生产-验证闭环。

当前 AI Coding 面临两个质量缺口：
- **过程缺口**：AI 的决策上下文困在聊天历史，中断后无法恢复，关键决策被静默做出 → Sopify 解决
- **验证缺口**：AI 产出的代码缺乏独立审查，同 session review 继承作者偏见 → CrossReview 解决

知识工程（业务图谱、缺陷规则）是两者的**增强层** — 注入业务知识可以让生产更准、验证更深，但闭环本身不依赖知识工程即可运转。

---

## 2. 双轮驱动架构

Sopify 和 CrossReview 分别占据生产侧与验证侧，形成闭环。

```
    +--------------------------------------------------------+
    | 知识工程底座                                           |
    | blueprint/knowledge/                                   |
    | graphify + spec-kit + ADR + 缺陷规则                   |
    +--------------------------------------------------------+
                   |                           |
   +-----------------------+          +--------------------------+
   | SOPIFY (生产侧)       |          | CROSSREVIEW (验证侧)     |
   |                       |          |                          |
   | · 需求分析            |          | · ReviewPack             |
   | · 方案设计            | Phase 4  | · 隔离审查               |
   | · 开发实施            | -------> | · 确定性裁决             |
   |                       |          |                          |
   | · checkpoint          | <------- | · 双指纹追踪             |
   | · 知识库同步          | findings | · 8项 release gate       |
   +-----------------------+          +--------------------------+
```

### 2.1 Sopify — 可恢复、可审计的 AI 编码工作流

**核心定义：** 可恢复、可审查、跨会话的 AI 编码工作流。决策上下文不再被困在聊天历史中；每次会话从上次停点恢复，而非从零开始。

**与集成相关的关键能力：**
| 能力 | 与集成的关系 |
|------|-------------|
| 三阶段工作流 + checkpoint | Phase 4a/4b 的集成锚点 — CR findings 在 develop 后注入 |
| 知识层级 blueprint/plan/history | blueprint/knowledge/ 是知识工程的统一存放层 |
| 多宿主支持 | CR host-integrated 模式需要宿主执行隔离上下文 |

**当前成熟度：** 核心工作流深度验证 ✅，多宿主稳定 ✅

### 2.2 CrossReview — 上下文隔离的独立验证

**核心定义：** AI 编程时代的独立验证基础设施

**核心洞察：**
> 你不需要换一个模型，只需要换一个上下文。Same model, clean session, real findings.

**与集成相关的关键能力：**
| 能力 | 与集成的关系 |
|------|-------------|
| ReviewPack 协议 | context_files 字段可从 blueprint/knowledge/ 自动填充 |
| 确定性管线 | Normalizer/Adjudicator 规则可从 knowledge/rules/ 加载 |
| host-integrated 模式 | render-prompt + ingest = 宿主零 API 成本集成 |

**当前成熟度：** v0-alpha，核心管线完整 ✅，release gate 73% 达标 ⚠️

**vs 竞品差异化：**
| 维度 | Claude ultrareview | Copilot CR | code-review Skill | CrossReview |
|------|-------------------|-----------|-------------------|-------------|
| 上下文隔离 | ✓ 云端 | ✗ 共享 PR session | ✗ 共享会话 | ✓ ReviewPack 协议 |
| 判定层 | LLM 全程 | LLM 全程 | LLM 全程 | **规则化**（确定性） |
| 可追踪性 | 无 fingerprint | 无 | 无 | SHA-256 双指纹 |
| 质量度量 | 不透明 | 不透明 | 无 | **8 指标 release gate** |
| 成本 | 5-20 agent × 10-25 min | 套餐 | 1 次 LLM call | 1 reviewer × 1 call |
| 输出格式 | 自然语言 | 平台内嵌评论 | 自然语言 | **结构化 JSON** |
| 可移植性 | ✗ 绑定 Anthropic | ✗ 绑定 GitHub | ⚠️ 绑定 Copilot CLI | ✓ 任何宿主 |

**三类产品的本质差异：**

| 类型 | 代表 | 定位 | 核心限制 |
|------|------|------|---------|
| **快速护栏** | code-review Skill | 开发过程中的即时自检 | 无隔离、无度量、reviewer 继承当前会话偏见 |
| **平台级覆盖** | ultrareview / Copilot CR | 企业 PR 流自动化 | 全 LLM 不透明、vendor lock-in、高成本 |
| **可检查的独立验证** | CrossReview | AI 输出的质量保障基础设施 | 确定性管线 + 可审计 + 质量可度量 |

**host-integrated 模式的结构性优势：**

CrossReview 的 `render-prompt + ingest` 路径实现了**审查管线分离**：

```
竞品:    diff ──→ [LLM 黑箱] ──→ 评论
                  ↑ 不可检查

CR:      diff ──→ [Pack] ──→ [render-prompt] ──→ [宿主 LLM] ──→ [ingest] ──→ 结构化结果
                  ↑ 可检查    ↑ 无 LLM 调用       ↑ 零额外成本     ↑ 确定性     ↑ 可度量
```

- **零 API 成本** — 用宿主已有的 LLM quota，用户无采纳摩擦
- **宿主中立** — 任何宿主只需执行 2 个 shell 命令
- **管线可见** — 每一步可独立检查、替换、度量
- **协议标准化** — ReviewPack 可能成为 "AI code review 的 LSP"

竞品无法模仿这条路线 — 它们的卖点恰好是"全自动、不用管细节"；而管线可见性是 CrossReview 的**结构性选择**，不是功能缺失。

---

## 3. 知识工程底座（研究态 → 落地前）

**核心架构：**
```
本地项目（代码 + Spec + ADR + 规范）
        ↓  知识图谱生成 (graphify)
本地知识资产（blueprint/knowledge/ 结构化图谱）
        ↓  Skills / 上下文注入
AI 工具（Sopify 生产 + CrossReview 验证）
        ↓
带业务语义的 AI 输出
```

**4 阶段路线：**
| Phase | 目标 | 就绪度 |
|-------|------|--------|
| Phase 1 | 图谱生成工具 → blueprint/knowledge/ 本地资产 | 90% (graphify v0.4.23 可用) |
| Phase 2 | Skills 集成 → AI 上下文注入 | 70% (Sopify Skills 就绪，消费层待建) |
| Phase 3 | 规则沉淀 → 缺陷知识库 | 40% (管线不存在，需新建) |
| Phase 4 | 反馈闭环 → 知识自进化 | 25% (纯概念态) |

---

## 4. 生态组件矩阵

| 组件 | 角色 | 版本 | 与主线关系 |
|------|------|------|-----------|
| **graphify** | 知识图谱引擎 | v0.4.23 ✅ | Phase 1 核心工具，25 语言 AST + LLM 语义提取 |
| **spec-kit** | 规格驱动开发 | Active 🔄 | 业务知识主输入，spec.md 作为图谱一等节点 |
| **hermes-agent** | 自进化 Agent | v0.10.0+ ✅ | Phase 2-4 交付基础设施，学习循环可桥接 |
| **helloagents** | 质量工作流 | v3.0.7 ✅ | 模块文档推断，需与 .sopify-skills/ 对齐 |
| **superpowers** | 开发方法论 | v1.0 ✅ | 过程框架参考，spec→plan→verify 流程一致 |
| **andrej-karpathy-skills** | 编码纪律 | v1.0 ✅ | 质量护栏，防止知识产物过度工程化 |
| **ai-daily-brief** | 信息筛选 | v1.0 ✅ | 两阶段 LLM 过滤模式可参考用于规则验证 |

---

## 5. 收敛性评估

### 5.1 收敛证据

| 维度 | Sopify | CrossReview | 契合度 |
|------|--------|-------------|--------|
| 核心理念 | 可恢复、可审计的工作流 | 可复现、可追踪的审查 | ✅ 同源 |
| 架构哲学 | harness engineering | deterministic pipeline | ✅ 一致 |
| 知识依赖 | `.sopify-skills/blueprint/` | ReviewPack context_files | ✅ 可接通 |
| 集成路径 | Phase 4a/4b 明确定义 | Sopify plugin 有 skill.yaml | ✅ 已规划 |
| 质量观 | checkpoint 拦截 + 用户确认 | 8 项 release gate | ✅ 互补 |
| 知识消费 | blueprint + plan 指导生产 | context_files 指导审查 | ✅ 双端复用 |

### 5.2 整体判定

**收敛度：高** — 两个项目围绕同一核心命题（AI 编程质量保障）展开，分工明确、接口清晰、知识底座共享。不存在方向性分歧或重复建设。

**潜力评级：⭐⭐⭐⭐☆ (4/5)** — 差异化定位清晰：Sopify 保障生产过程，CrossReview 保障产出验证。短中期关键变量是 CR release gate 与 Phase 4a dogfood；知识工程只是长期增强层。

**风险标识：**
- ⚠️ Sopify 协议复杂度有膨胀趋势（runtime gate 校验链已 4 项条件 + 多种 response mode）
- ⚠️ CrossReview release gate 尚有 1 项指标未达（unclear_rate 0.200 > 0.150）
- ⚠️ 知识工程 P2 基础设施（BlueprintEnhancer ABC）0% 完成，阻塞知识工程链路，不阻塞 CR/Sopify 主线

---

## 6. 文档所有权与一致性

### 分层所有权

| 信息域 | Source of Truth | 本文档做法 |
|--------|----------------|-----------|
| 生态集成视图、优先级、飞轮设计 | **本文档** | 直接定义 |
| Sopify 工作流协议、checkpoint 细节 | sopify-skills 总纲 | 引用结论，不重复协议 |
| CrossReview 管线、release gate 规则 | cross-review 总纲 | 引用指标，不重复规则 |
| 集成接口（Phase 4a/4b） | 双方共有 | 各自维护自己侧的接口 |

### Sync Check

> 当 Sopify 或 CrossReview 做架构级变更时，需同步检查本文档一致性。

| 子项目 | 引用版本/状态 | 上次同步 |
|--------|-------------|---------|
| Sopify 总纲 | v2026-04-14 (core deep verified) | 2026-04-26 |
| CrossReview 总纲 | v0-alpha (v0-04a pending) | 2026-04-26 |
| 知识工程研究 | knowledge-engineering-research.md | 2026-04-26 |
