# Sopify × CrossReview 生态总纲 — 优先级排序与行动路线

> 生成日期：2026-04-26
> **修订：2026-04-26 一致性对齐**
> - 知识工程（BlueprintEnhancer / GraphifyEnhancer / knowledge schema）从 P1 降为 P2/P3
> - CR 发布关键路径（unclear_rate → release gate → v0-05/06/07）统一为 P0
> - Phase 4b 统一为"🧊 冻结，不进入 0-6 个月承诺"
> - 新增 Source of Truth 声明与机器可校验依赖图
> - `blueprint/knowledge/` 目录 schema 随知识工程一起降 P2，不提前改 Sopify 目录结构
> - GraphifyEnhancer（知识资产生成）与 Graphify Advisory Skill（Sopify Phase 5 用户插件）概念拆分
> - 新增 Sopify ADR-017 对齐：Action Schema Boundary，不维护用户话术白名单；生态总纲只记录跨项目影响
> **修订：2026-04-27 状态同步**
> - CR release gate 已 9/9 通过 + self_hosting_pool_limit_ok 通过（`blocking_pass: true`）
> - CR P0 焦点从 unclear_rate / release gate 转为 v0-05 / v0-06 / v0-07 发布就绪链
> - CR debug JSONL trace 列为 v0-07 后第一个维护迭代，默认关闭，不阻塞 PyPI 首发

## Source of Truth 声明

| 总纲 | 权威范围 | 维护职责 |
|------|---------|---------|
| **Sopify 总纲** (`sopify-skills/.sopify-skills/plan/...`) | Sopify 产品方向、Phase 定义、Protocol 设计、ADR | Sopify 项目独立维护 |
| **CR 总纲** (`cross-review/.sopify-skills/plan/...`) | CrossReview 产品方向、eval 体系、release gate、通道设计 | CrossReview 项目独立维护 |
| **生态总纲** (本文档) | 跨项目排序、依赖图、里程碑对齐 | 仅维护跨项目排序和依赖关系；不覆盖单项目内部设计决策 |

> **冲突解决**：单项目内部事实以对应项目总纲为准。生态总纲只在跨项目依赖和排序上有约束力。

---

## 优先级总览

```
P0 (发布就绪，立即)   P1 (关键路径)         P2 (知识工程)         P3 (增强，2月+)       🧊 冻结
─────────────────    ─────────────────    ─────────────────   ─────────────────   ─────────────────
CR v0-05/06/07        PyPI 0.1.0           BlueprintEnhancer   knowledge-graph     CR Phase 4b
发布就绪链             Sopify Phase 4a      ABC 实现             -query Skill         (不进入 0-6 月)
CR debug JSONL         (+Convention验证)   GraphifyEnhancer    规则沉淀管线
trace（v0-07后）       Protocol Step 1      MVP                 反馈闭环设计
                       (ADR-016 基础)     blueprint/knowledge 跨项目知识联邦
                      Protocol Step 3      schema 定义         协议瘦身审计
                       (ADR-017 约束)     spec→图谱节点桥接
                                            图谱自动刷新
```

---

## 详细任务分解

### ━━━ P0：发布就绪（立即处理）━━━

#### T-P0-1：CrossReview unclear_rate 修复 `✅ 完成`
- **当前状态：** 0.133 ≤ 0.150，release gate 已通过（2026-04-27 v0-04a 修复后）
- **影响范围：** 已解除 CR v0 发布和 Sopify Phase 4a 的 release gate 阻塞
- **行动：**
  1. 复核 unclear finding 并完成 003/018/019 重分类
  2. 补充 prompt anti-hedge 指令
  3. 全量重跑评测，确认 ≤ 0.150
- **产出：** unclear_rate 0.133，v0-04a 关闭
- **依赖：** 无
- **估计：** 已完成

#### T-P0-2：CR v0-04b 全量 release gate 重跑 `✅ 完成`
> 原 T-P1-4，提升为 P0（CR 发布关键路径）

- **背景：** unclear_rate 修复后需全量验证；2026-04-27 已完成
- **行动：**
  1. 确认 fixture 池就绪（v0-02 产出）
  2. 运行完整 eval harness
  3. 验证 9 项 release gate 全部达标：
     - manual_recall ≥ 0.80
     - precision ≥ 0.70
     - invalid_findings_per_run ≤ 2
     - max_invalid_single_run ≤ 5
     - unclear_rate ≤ 0.15
     - context_fidelity ≥ 0.80
     - actionability ≥ 0.90
     - failure_rate ≤ 0.10
     - fixture_count ≥ 20
  4. 如有未达标项，分析原因并修复（当前无阻断项）
- **产出：** 9/9 release gate 通过 + self_hosting_pool_limit_ok 通过
- **依赖：** T-P0-1
- **估计：** 已完成

#### T-P0-3：CR v0-05 / v0-06 / v0-07 发布就绪链
> 从 T-P1-1 拆出并提升为 P0（CR 发布关键路径，与 CR 总纲一致）

- **背景：** release gate 通过后，仍需补齐用户可运行的发布就绪能力
- **行动：**
  1. v0-05：human-readable 输出 (`--format human`)
  2. v0-06：one-stop verify (`crossreview verify --diff`)
  3. v0-07：发布就绪判定
- **产出：** CrossReview v0 release ready
- **依赖：** T-P0-2
- **估计：** 5-7 天

#### T-P0-4：CR debug JSONL trace `P1 / v0-07 后第一个维护迭代`
- **背景：** 本地 JSONL trace 对 release gate、unclear_rate、adjudication 调试有直接价值，但不应阻塞 PyPI 首发
- **行动：**
  1. 增加默认关闭的 `--debug-jsonl <path>` 输出
  2. 最小记录 `fixture_id / stage / reason_code / decision / metric_impact / input_hash / output_summary`
  3. 仅用于调试，不接外部观测平台，不承诺长期兼容 schema
- **产出：** 本地调试 trace，可用于后续指标回归定位
- **依赖：** T-P0-3
- **估计：** 0.5-1 天

---

### ━━━ P1：关键路径（CR 发布后立即）━━━

#### T-P1-1：CrossReview v0 正式发布
> 原 T-P2-4，提升为 P1（P0 gate 通过后的直接下一步）

- **背景：** v0-07 发布就绪后的分发收尾工作
- **行动：**
  1. PyPI 0.1.0 发布
  2. 文档 + 示例
  3. 安装路径验证
- **产出：** `pip install crossreview` 可用
- **依赖：** T-P0-3
- **估计：** 2-3 天

#### T-P1-2：Sopify Phase 4a — CR Advisory 集成 + Convention 模式验证
> 原 T-P2-5，提升为 P1（CR 发布后立即启动）
> **确认既有共识 (2026-04-28 修订)**：Phase 4a 仅做 advisory skill（SKILL.md + skill.yaml），不做 bridge.py，不做 pipeline_hooks。默认 host-integrated 路径为 `pack -> render-prompt -> 宿主隔离审查 -> ingest --format human`；`verify --diff --format human` 仅作为 standalone fallback，需 reviewer config / API key。Phase 4a scope 三份总纲按此口径对齐。
> **战略标注 (2026-04-27)**：Phase 4a 同时作为 ADR-016 Convention 模式的首次实战验证。LLM 不经 runtime 编排，仅靠 SKILL.md 表单式指令自主调用外部 CLI。

- **背景：** CrossReview 作为 Sopify develop 后的可选审查
- **战略双重定位：**
  1. 产品目标：Sopify develop 后可选触发 CR advisory 审查
  2. 战略验证：Convention 模式（ADR-016）首次实战检验
- **Convention 验证指标**（dogfood 完成后评估）：
  - LLM 是否按 SKILL.md 指令可靠调用 CLI？
  - failure mode 分类：忘记调用 / 参数错误 / verdict 处理偏离 / 回退路径不触发
  - 3 项目 dogfood 中 Convention 模式成功率
  - 是否暴露 Validator (Protocol Step 2) 的真实需求？
- **验证结果反馈至 Protocol-first 路线：**
  - 成功率高 → Protocol-first 可行，Step 2 维持信号驱动
  - 出现系统性偏离 → Step 2 (Validator) 升级为 P1
  - 特定 failure mode → 定向调整 SKILL.md 格式或 Step 3 优先级
- **⚠️ 同步更新：** 实现时需同步更新：① Sopify 总纲技能引用表（加 cross-review skill）；② CR 总纲 host_adapter 集成方式；③ CR ReviewPack context_files 来源策略
- **行动：**
  1. 创建 `.agents/skills/cross-review/` Skill 定义
  2. skill.yaml：advisory mode，post_develop 触发
  3. SKILL.md：编排 `crossreview pack --diff` → `crossreview render-prompt` → 宿主隔离审查 → `crossreview ingest --format human`
  4. 测试：develop → findings 展示 → 用户决策
- **产出：** Sopify develop 完成后可触发 CrossReview 审查
- **依赖：** T-P1-1
- **估计：** 3-5 天

#### T-P1-3：Sopify ADR-017 — Action Schema Boundary 对齐
> Sopify 内部设计以 Sopify 总纲为准；生态总纲只记录跨项目影响。

- **背景：** 风险自适应打断不能依赖持续维护用户话术白名单
- **行动：**
  1. Sopify 总纲新增 ADR-017：LLM 只提出结构化 action proposal，Core/Validator 基于机器事实授权
  2. Protocol Step 3 使用有限 action schema + side_effect policy，不扩用户自然语言名单
  3. Phase 0.1 `risk_engine_upgrade` 启动前按 ADR-017 重审，RiskRule 只做 hard-risk detector
  4. Protocol Step 3 补齐 `action_schema_version` / `supported_actions` / 最小 action audit event，供未来 bridge.py 或宿主查询
  5. Phase 4a advisory 不受阻塞；未来 Phase 4b bridge verdict 必须输出结构化 checkpoint/action proposal
- **产出：** Sopify 风险打断与 plugin checkpoint 边界统一
- **依赖：** 无（不阻塞 T-P0-* / T-P1-1 / T-P1-2）
- **估计：** 文档级 0.5 天；实现随 Protocol Step 3 或事件触发

#### T-P1-5：Sopify ADR-018 — Legacy Surface Retirement 对齐
> runtime / host cleanup 由 Sopify 总纲 ADR-018 负责；生态总纲只追踪跨项目依赖和优先级。

- **背景：** runtime / engine / host 改造会产生遗留面（旧入口、旧测试、旧文档）；需要统一治理原则
- **行动：**
  1. Sopify 总纲新增 ADR-018：改造时必须声明新机器事实来源、被替代旧入口、删除顺序、暂时保留原因
  2. Trae CN surface sunset（由 Sopify 总纲 T-cleanup-* 执行，生态总纲只追踪完成状态）
  3. ADR-018 预见性声明：当 ADR-017 action schema 取代 keyword match 后，旧话术名单按 sunset → removed 清理
- **产出：** Sopify runtime / host 遗留面治理统一
- **依赖：** 无（不阻塞 T-P0-* / T-P1-1 / T-P1-2 / T-P1-3）
- **估计：** 文档级 0.5 天；Trae cleanup 1-2 天

#### T-P1-6：Protocol Step 1 — 协议文档提取
> ADR-016 Protocol-first 顶层战略的执行基础。Sopify 内部执行，生态追踪可见性。

- **背景：** ADR-016 确认 Protocol 是 Sopify 不可替代的核心价值层，但协议规范从未被独立提取为文档。Protocol Step 1 是 Step 2 (Validator) 和 Step 3 (Action Schema) 的链式前置。
- **行动：**
  1. 提取 plan schema：目录约定、文件命名、background/design/tasks 结构
  2. 提取 state schema：current_handoff.json / current_run.json / gate_receipt 契约字段
  3. 提取 lifecycle 约定：plan → history 生命周期、归档规则、blueprint 更新触发
  4. 提取 SKILL.md 编排规范：表单式格式、`[ACTION:]` 模式、分支结构、弱模型下界要求
  5. 提取 checkpoint schema：4 种内置 checkpoint 的字段约定与 side_effect 标注
- **产出：** docs/protocol/ 或 .sopify-skills/protocol/，独立可读的协议规范
- **依赖：** 无
- **验证：** CR 团队作为外部读者验证可理解性；至少 1 个非 Sopify 维护者能读懂并回答"如何接入"
- **约束：** 纯文档零代码，不抢 P0；不与 Phase 0.2-B/C 或 CR gate 冲突
- **估计：** 2-3 天

---

### ━━━ P2：知识工程（不阻塞 CR/Sopify 任何阶段）━━━

> **对齐决策 (2026-04-26)：** 知识工程从 P1 降为 P2/P3。不作为 CR 发布或 Sopify 任何阶段的前置条件。`blueprint/knowledge/` 目录 schema 不提前改 Sopify 目录结构，待知识工程启动时再定。

#### T-P2-1：BlueprintEnhancer ABC 基础架构
> 原 T-P1-1，降为 P2

- **背景：** 知识工程基础架构，阻塞知识工程后续任务，但不阻塞 CR/Sopify 主线
- **行动：**
  1. 实现 `installer/blueprint_enhancer.py`：
     - ABC 基类：`name`, `is_available()`, `validate_enhancer_config()`, `generate()`, `update()`, `render_auto_sections()`
     - `ENHANCER_REGISTRY` + `@register_enhancer` 装饰器
     - `EnhancerConfigError` 独立异常
     - `inject_auto_sections()` 注入引擎
  2. 修改 `runtime/config.py`：
     - `blueprint_enhancers: {}` 加入 `DEFAULT_CONFIG`
     - 加入 `_ALLOWED_TOP_LEVEL`
     - 创建 `_validate_blueprint_enhancers()`
  3. 更新 `sopify.config.yaml` 配置项
- **产出：** BlueprintEnhancer 可注册、可调用、可配置
- **验收：** 至少 1 个 enhancer 可注册并通过 `is_available()` / config validate / `generate()` dry-run；禁用 enhancer 时现有 Sopify 流程行为不变。
- **依赖：** 无
- **估计：** 5-7 天

#### T-P2-2：GraphifyEnhancer MVP
> 原 T-P1-2，降为 P2
> **概念区分**：GraphifyEnhancer 是知识资产生成（知识工程内部）；Graphify Advisory Skill 是 Sopify 侧用户可调用插件（Sopify Phase 5），两者独立。

- **背景：** 首个 enhancer 实现
- **行动：**
  1. 实现 `installer/enhancers/graphify_enhancer.py`：
     - `is_available()`: pkg:graphifyy → pkg:graphify → import 链式检测
     - `generate()`: File → AST → extract → graph → cluster → label
     - `update()`: 增量 + 全量回退
     - `render_auto_sections()`: 图谱摘要注入 blueprint
  2. 实现 `scripts/blueprint_enhance.py`：
     - `--strict` / `--list` 参数
     - 编排 enhancer 执行顺序
  3. 端到端验证：真实项目生成 `blueprint/knowledge/graph.json`
- **产出：** graphify 可作为 Sopify enhancer 插件运行
- **验收：** 在至少 1 个真实项目生成 `blueprint/knowledge/graph.json`；缺少 graphify 时给出可见降级，不阻断 Sopify 主线。
- **依赖：** T-P2-1
- **估计：** 5-7 天

#### T-P2-3：blueprint/knowledge/ schema 定义
> 原知识工程 T-P1-3，降为 P2（随知识工程一起启动，不提前改 Sopify 目录结构）

- **背景：** graphify 等机器派生知识需要明确的存放位置和 schema 规范
- **⚠️ 同步更新：** 实现时需同步更新 Sopify 总纲 `CLAUDE.md` A5 目录结构（加入 `knowledge/` 子目录）
- **行动：**
  1. 定义 blueprint/knowledge/ 目录规范：
     ```
     .sopify-skills/blueprint/knowledge/
     ├── graph.json          # 项目语义图谱（graphify 生成）
     ├── modules.md          # 模块职责与关系（图谱 + 推断）
     ├── flows.md            # 核心业务流程（图谱 + spec）
     ├── rules/              # 结构化缺陷规则
     └── adr/                # 架构决策索引
     ```
  2. 定义 graphify → blueprint/knowledge/ 桥接合约（输出格式、字段映射）
  3. 定义可重建性规则：knowledge/ 下所有内容可删除后通过 enhancer 重新生成
  4. 文档化：schema 版本、兼容性承诺
- **产出：** blueprint/knowledge/ schema v0.1 文档
- **依赖：** 无（可与 T-P2-1 并行）
- **估计：** 2-3 天

#### T-P2-4：spec.md → 图谱节点桥接
> 原 T-P2-2

- **背景：** spec-kit 的业务规格是知识工程核心输入
- **行动：**
  1. 定义 spec.md 作为图谱一等节点的导入规范
  2. 提取 spec 边（依赖关系、假设、验收标准）
  3. 桥接到 `blueprint/knowledge/graph.json` 的 node/edge schema
  4. 验证：spec 片段可通过 `/graphify spec-query` 查询
- **产出：** 业务意图可被 AI 通过图谱查询
- **依赖：** T-P2-2, T-P2-3
- **估计：** 5-7 天

#### T-P2-5：图谱自动刷新机制
> 原 T-P2-3

- **背景：** 当前 graphify 需手动执行 `/graphify .`
- **行动：**
  1. 设计 `graphify watch` 增量模式（监听 src/ + docs/ 变更）
  2. 建立 GitHub Actions workflow（commit 触发图谱更新）
  3. SHA256 缓存机制确保只处理变更文件
  4. 失败降级策略（自动刷新失败时不阻断开发流）
- **产出：** 图谱在代码变更后自动更新
- **依赖：** T-P2-2
- **估计：** 5-7 天

---

### ━━━ P3：增强（2 个月后）━━━

#### T-P3-1：knowledge-graph-query Skill
> 原 T-P2-1，降为 P3

- **背景：** 知识工程核心交付
- **行动：**
  1. 创建 `knowledge-graph-query` Skill（skill.yaml + SKILL.md）
  2. 封装 graphify 查询能力：
     - `/graphify query` — 语义查询
     - `/graphify path` — 路径追踪
     - `/graphify explain` — 模块解释
  3. 定义触发策略：
     - design 阶段：自动注入相关模块关系
     - develop 阶段：自动注入调用链和依赖
     - review 阶段：自动注入变更影响面
  4. 在 Codex + Claude 双宿主验证
- **产出：** AI 在 design/develop 时可查询项目知识图谱
- **验收：** Codex + Claude 均可完成一次查询调用；查询结果能被 design/develop 阶段引用，且缺图谱时可降级为无注入。
- **依赖：** T-P2-2
- **估计：** 7-10 天

#### T-P3-2：规则沉淀管线 MVP
> 原 T-P3-1

- **背景：** 知识工程 Phase 3
- **行动：**
  1. 手动沉淀 20-50 条高频缺陷规则（Bad/Good Case JSON）
  2. 设计 incident → 结构化规则的提取模板
  3. 规则存储在 `blueprint/knowledge/rules/` 目录
  4. 创建规则查询 Skill（按 category/severity 检索）
  5. 验证：规则注入 CR context → 评审质量提升
- **产出：** 结构化规则集 + 注入验证
- **依赖：** T-P3-1
- **估计：** 2-3 周

#### T-P3-3：反馈闭环设计
> 原 T-P3-2

- **背景：** 知识工程 Phase 4
- **行动：**
  1. 定义反馈数据采集规范（user/feedback.jsonl schema）
  2. 设计"开发者修正 → 规则更新"的触发机制
  3. 度量体系：图谱准确率、规则命中率、CR 采纳率
  4. 原型：Hermes agent 学习循环桥接
- **产出：** 反馈闭环设计文档 + 原型
- **依赖：** T-P3-2
- **估计：** 2-3 周

#### T-P3-4：跨项目知识联邦
> 原 T-P3-4

- **背景：** 当前知识完全本地化，无跨项目关联
- **行动：**
  1. 设计跨服务 API 契约 → 共享边
  2. 共享模块跨仓库 → 联邦图谱
  3. 组织级规则库归一化
- **产出：** 跨项目知识查询能力
- **依赖：** T-P2-4, T-P3-2
- **估计：** 4-6 周

#### T-P3-5a：Complexity Budget Guard `P1`
> 原 T-P3-5 拆分（P3 → P1）
> ID 保留原 T-P3-5 追溯，实际优先级以 `priority: P1` 为准。

- **背景：** 协议复杂度持续增长（7 状态文件、8 宿主动作 × 28 子动作、22 helper 脚本、24+ 系统提示规则），每个新 ADR 只增不减
- **行动：**
  1. 在 Sopify 总纲"优先级约束"中增加 complexity budget guard：新增 protocol 字段、状态文件、runtime helper、host action、系统提示规则、ADR，必须同时说明替代了什么、能否删旧、是否增加 complexity debt；不能说明的默认不进入 P0/P1 主线
  2. 预算覆盖 4 类：state files / helper entrypoints / host actions / prompt-runtime rules
- **产出：** 复杂度预算规则写入 Sopify 总纲
- **依赖：** 无
- **估计：** 0.5 天

#### T-P3-5b：Protocol Slim Audit `P1`
> 原 T-P3-5 拆分（P3 → P1），ADR-017 Step 3 完成后执行
> ID 保留原 T-P3-5 追溯，实际优先级以 `priority: P1` 为准。

- **背景：** ADR-017 Step 3 会新增 action schema 层，同时预计可清理 keyword alias sets；等它落地后再审，避免重构两次
- **行动：**
  1. 梳理当前所有 gate 条件、response mode、handoff action
  2. 识别可合并的校验链（减少 evidence 字段数）
  3. 评估哪些状态文件、helper、host action、系统提示规则可精简
  4. 输出删除/合并建议（不预先承诺具体合并形态）
- **产出：** 瘦身方案
- **依赖：** ADR-017 Protocol Step 3 完成
- **激活条件：** ADR-017 Protocol Step 3 implementation completed
- **估计：** 1 周

---

### ━━━ 🧊 冻结（不进入 0-6 个月承诺）━━━

#### T-FROZEN-1：CrossReview Phase 4b — Runtime 深度集成
> 原 T-P3-3，统一为冻结
> **对齐决策 (2026-04-26)**：三份总纲统一为"🧊 冻结，不进入 0-6 个月承诺"。不使用"P3 增强"等暗示已排期的语言。

- **背景：** CR 与 Sopify checkpoint 深度绑定
- **行动（启动后）：**
  1. 设计 bridge.py 桥接合约
  2. verdict → checkpoint proposal 映射
  3. 修改 → 重审循环（≤2 轮）
  4. findings 自动回流 `.sopify-skills/` 知识库
- **启动条件（全部满足）：**
  1. Sopify Phase 3 就绪
  2. Phase 4a 价值验证通过
  3. ≥3 个 dogfood 数据证明 advisory 不够用
- **产出：** CR 与 Sopify 深度集成
- **依赖：** T-P1-2 运行稳定后 + Sopify Phase 3

---

## 依赖关系图

```
T-P0-1 ✅ (CR unclear_rate)
    └──→ T-P0-2 ✅ (release gate 全量重跑)
            └──→ T-P0-3 (v0-05/06/07 发布就绪)
                    ├──→ T-P0-4 (debug JSONL trace, 默认关闭, v0-07 后)
                    └──→ T-P1-1 (CR PyPI 0.1.0)
                            └──→ T-P1-2 (Sopify Phase 4a + Convention 验证)
                                    ├──→ T-FROZEN-1 (CR Phase 4b, 🧊 冻结)
                                    └──→ Convention 验证数据 → Protocol Step 2 激活信号

T-P1-6 (Protocol Step 1, ADR-016 基础, 纯文档)  ← P1，不抢 P0，与 Phase 0.2-B/C 并行
    ├──→ Protocol Step 2 (信号驱动：Step 1 + Phase 4a 验证数据 或 2次校验需求 或 新宿主接入)
    └──→ Protocol Step 3 (通过 T-P1-3 ADR-017 已部分覆盖)

T-P1-3 (Sopify ADR-017 Action Schema Boundary)  ← 独立，不阻塞 CR P0
    ├──→ Protocol Step 3
    ├──→ Phase 0.1 启动前重审
    └──→ 未来 Phase 4b bridge proposal 约束

T-P1-5 (Sopify ADR-018 Legacy Surface Retirement)  ← 独立，不阻塞 CR P0
    ├──→ Trae sunset (T-cleanup-2)
    ├──→ runtime entrypoint inventory (T-cleanup-1)
    └──→ 当 ADR-017 实施后，旧话术名单按 sunset → removed 清理

T-P2-1 (BlueprintEnhancer ABC)
    └──→ T-P2-2 (GraphifyEnhancer MVP)
            ├──→ T-P3-1 (知识查询 Skill)
            │       └──→ T-P3-2 (规则沉淀)
            │               └──→ T-P3-3 (反馈闭环)
            ├──→ T-P2-4 (spec 桥接)
            │       └──→ T-P3-4 (跨项目联邦)
            └──→ T-P2-5 (图谱自动刷新)

T-P2-3 (blueprint/knowledge/ schema)  ← 可与 T-P2-1 并行
    └──→ T-P2-4

T-P3-5a (Complexity Budget Guard, P1)  ← 独立，立即生效
T-P3-5b (Protocol Slim Audit, P1)  ← ADR-017 Step 3 完成后执行
```

**关键约束：** 知识工程链（T-P2-*）与 CR 发布链（T-P0/P1-*）无依赖关系，可独立推进但不作为 CR/Sopify 任何阶段的前置。

---

## 机器可校验依赖图

```yaml
# dependency-graph.yaml
# Source of Truth: 生态总纲（跨项目排序与依赖）
# 单项目内部事实以对应项目总纲为准

tasks:
  T-P0-1:
    title: CR unclear_rate 修复
    priority: P0
    depends_on: []
    project: cross-review
    status: completed
  T-P0-2:
    title: CR release gate 全量重跑
    priority: P0
    depends_on: [T-P0-1]
    project: cross-review
    status: completed
  T-P0-3:
    title: CR v0-05/06/07 发布就绪链
    priority: P0
    depends_on: [T-P0-2]
    project: cross-review
  T-P0-4:
    title: CR debug JSONL trace
    priority: P1
    depends_on: [T-P0-3]
    project: cross-review
    note: "v0-07 后第一个维护迭代；默认关闭；不阻塞 PyPI 首发"
  T-P1-1:
    title: CR PyPI 0.1.0 发布
    priority: P1
    depends_on: [T-P0-3]
    project: cross-review
  T-P1-2:
    title: Sopify Phase 4a CR Advisory + Convention 模式验证
    priority: P1
    depends_on: [T-P1-1]
    project: sopify-skills
    strategic_validation: "ADR-016 Convention mode first real-world test"
    validation_metrics:
      - "SKILL.md 遵循率"
      - "LLM failure mode 分类"
      - "是否触发 Step 2 Validator 需求"
  T-P1-3:
    title: Sopify ADR-017 Action Schema Boundary
    priority: P1
    depends_on: []
    project: sopify-skills
    scope: "architecture_constraint"
    blocks_cr_p0: false
    constrains:
      - Protocol Step 3
      - Phase 0.1 risk_engine_upgrade
      - future Phase 4b bridge proposal
  T-P1-5:
    title: Sopify ADR-018 Legacy Surface Retirement
    priority: P1
    depends_on: []
    project: sopify-skills
    scope: "governance_constraint"
    blocks_cr_p0: false
    constrains:
      - Sopify ADR-018 cleanup completion
      - future keyword alias sets retirement (post ADR-017)
    external_refs:
      - sopify-skills:T-cleanup-1..T-cleanup-4
  T-P1-6:
    title: Protocol Step 1 — 协议文档提取
    priority: P1
    depends_on: []
    project: sopify-skills
    scope: "protocol_foundation"
    blocks_cr_p0: false
    note: "ADR-016 顶层战略基础；纯文档零代码；Sopify 内部执行，生态追踪可见性"
    enables:
      - "Protocol Step 2 (Validator CLI, 信号驱动)"
      - "Protocol Step 3 (通过 T-P1-3 ADR-017)"
  T-P2-1:
    title: BlueprintEnhancer ABC
    priority: P2
    depends_on: []
    project: sopify-skills
  T-P2-2:
    title: GraphifyEnhancer MVP
    priority: P2
    depends_on: [T-P2-1]
    project: sopify-skills
  T-P2-3:
    title: blueprint/knowledge/ schema
    priority: P2
    depends_on: []
    project: sopify-skills
  T-P2-4:
    title: spec→图谱节点桥接
    priority: P2
    depends_on: [T-P2-2, T-P2-3]
    project: sopify-skills
  T-P2-5:
    title: 图谱自动刷新
    priority: P2
    depends_on: [T-P2-2]
    project: sopify-skills
  T-P3-1:
    title: knowledge-graph-query Skill
    priority: P3
    depends_on: [T-P2-2]
    project: sopify-skills
  T-P3-2:
    title: 规则沉淀管线 MVP
    priority: P3
    depends_on: [T-P3-1]
    project: sopify-skills
  T-P3-3:
    title: 反馈闭环设计
    priority: P3
    depends_on: [T-P3-2]
    project: sopify-skills
  T-P3-4:
    title: 跨项目知识联邦
    priority: P3
    depends_on: [T-P2-4, T-P3-2]
    project: sopify-skills
  T-P3-5a:
    title: Complexity Budget Guard
    priority: P1
    depends_on: []
    project: sopify-skills
    scope: "governance_constraint"
    blocks_cr_p0: false
  T-P3-5b:
    title: Protocol Slim Audit
    priority: P1
    depends_on: [T-P1-3]
    project: sopify-skills
    scope: "governance_constraint"
    blocks_cr_p0: false
    activation_condition: "ADR-017 Protocol Step 3 implementation completed"
    note: "ADR-017 Step 3 完成后执行"
  T-FROZEN-1:
    title: CR Phase 4b Runtime
    priority: frozen
    depends_on: [T-P1-2]
    frozen_reason: "不进入 0-6 个月承诺"
    activation_conditions:
      - "Sopify Phase 3 就绪"
      - "Phase 4a 价值验证"
      - "≥3 dogfood 数据证明 advisory 不够用"
    project: cross-review
```

---

## 里程碑

> 生态里程碑编号用于跨项目排序；CR 总纲中的 M0 / v0 Release Gate 对应本文 M1。

### M1：CR 发布就绪（+2 周）
- [x] CR unclear_rate ≤ 0.150
- [x] CR release gate 9/9 通过 + self_hosting_pool_limit_ok 通过
- [ ] v0-05 / v0-06 / v0-07 发布就绪链通过

### M2：CR 正式发布 + Phase 4a + Protocol 基础（+4 周）
- [ ] CR v0 正式发布（PyPI 0.1.0）
- [ ] Sopify Phase 4a advisory 跑通 + Convention 模式验证指标采集
- [ ] Sopify ADR-017 文档口径完成，Protocol Step 3 按 action schema 推进
- [ ] Protocol Step 1 文档完成（Sopify 协议规范独立可读）

### M3：知识工程基础（+8 周，不阻塞 M1/M2）
- [ ] BlueprintEnhancer ABC 可用
- [ ] GraphifyEnhancer 在真实项目生成图谱
- [ ] blueprint/knowledge/ schema v0.1 定义完成

### M4：知识注入（+12 周）
- [ ] knowledge-graph-query Skill 在 design/develop 阶段自动注入
- [ ] spec.md 作为图谱一等节点可查询

### M5：飞轮成型（+24 周）
- ✅ 规则沉淀 ≥ 20 条 + CR 消费验证
- ✅ 反馈闭环原型运转
- ✅ 生产 → 验证 → 知识 三环正反馈可观测

---

## 关键决策

### 已确认 (2026-04-26 一致性对齐)

| # | 决策 | 内容 |
|---|------|------|
| D-A1 | Source of Truth 划分 | Sopify→Sopify 总纲, CR→CR 总纲, 生态→跨项目排序+依赖图 |
| D-A2 | 知识工程优先级 | 降为 P2/P3，不作为 CR/Sopify 任何阶段前置 |
| D-A3 | Phase 4a scope | 确认既有共识：advisory only，无 bridge.py/pipeline_hooks |
| D-A4 | Phase 4b 口径 | 🧊 冻结，不进入 0-6 个月承诺 |
| D-A5 | blueprint/knowledge/ | 随知识工程 P2 启动时定义，不提前改 Sopify 目录结构 |
| D-A6 | GraphifyEnhancer vs Graphify Advisory Skill | 两个独立概念：前者是知识资产生成（P2），后者是 Sopify Phase 5 用户插件 |
| D-A7 | Action Schema Boundary | Sopify ADR-017：不维护用户话术白名单；LLM 只提议结构化 action，Core/Validator 基于机器事实、side_effect 和风险策略授权 |
| D-A8 | Legacy Surface Retirement | Sopify ADR-018：runtime/host cleanup 由 Sopify 总纲负责；生态总纲只追踪完成状态 |
| D-A9 | Protocol-first 执行介入 | ADR-016 顶层战略对齐：Protocol Step 1 升 P1；Phase 4a 标注 Convention 验证；生态层补可见性 |
| D-A10 | GitHub Org 迁移 | umbrella org 从 `sopify-ai` 迁移至 `evidentloop`（展示名 EvidentLoop）。Sopify → `evidentloop/Sopify`，CrossReview → `evidentloop/CrossReview`，ai-daily-brief → `Li-Sanze/ai-daily-brief`（不属于 evidence/verification 叙事）。org 名语义：evident(证据可见) + loop(三环飞轮)，覆盖生产-验证-知识三条线 |

### 待确认

| # | 决策 | 选项 | 推荐 | 影响 |
|---|------|------|------|------|
| D1 | blueprint/knowledge/ 中 graph.json 是否跟踪 | 纳入 vs gitignore | **纳入**（版本管理，团队共享） | 知识工程全链路 |
| D2 | BlueprintEnhancer 是否先做最小 shim | 完整 ABC vs 最小 shim | **完整 ABC**（避免后续迁移成本） | T-P2-1 时间线 |
| D3 | CR Phase 4a 触发策略 | 自动触发 vs 手动触发 | **LLM 自主判断**（advisory 模式） | 用户体验 |
| D4 | 图谱刷新频率 | 每次 commit vs 每日 vs 手动 | **每日 + 手动按需**（平衡成本） | T-P2-5 设计 |
| D5 | 规则沉淀冷启动策略 | 等自动管线 vs 先手动 | **先手动 20-50 条**（验证假设） | T-P3-2 启动时机 |
