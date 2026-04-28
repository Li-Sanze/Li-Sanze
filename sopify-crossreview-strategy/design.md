# Sopify × CrossReview 生态总纲 — 整合战略与架构设计

> 生成日期：2026-04-26

---

## 1. 战略全景：三环飞轮

整个生态的终极目标是形成 **生产 → 验证 → 知识** 三环正反馈飞轮：

```
            +--------------------------+
            | 知识工程底座             |
            | blueprint/knowledge/     |
            | graphify + spec + rules  |
            +--------------------------+
                 |              |
            注入上下文      反馈沉淀
                 |              |
   +------------------------+      +--------------------------+
   | SOPIFY (生产环)        |      | CROSSREVIEW (验证环)     |
   |                        | ---> |                          |
   | 需求 > 方案 > 开发     |      | ReviewPack > 隔离审查    |
   |                        | <--- | > findings               |
   +------------------------+      +--------------------------+
                     ^                    |
                     +---- findings 回流 -----+
```

**飞轮效应：**
1. Sopify 生产代码 → CrossReview 隔离验证
2. findings 回流知识库 → 沉淀为规则/模式
3. 知识库注入 Sopify → 下次生产更准、更符合业务
4. 更高质量的代码 → CrossReview 发现更深层问题（而非表面噪音）
5. 循环加速 → **越用越准**

> **当前阶段 (2026-04-26 修订)：** 生产 ↔ 验证闭环为 P0-P1 重心（CR release → Phase 4a）；知识环降为 P2/P3，不阻塞 CR/Sopify 任何阶段。

---

## 2. 三条主线及其交汇点

### 主线 A：Sopify 工作流演进

```
+------------------------+          +------------------------------+
| 当前态 (v2026-04)      |          | 目标态 (v2026-H2)            |
|                        |          |                              |
| 三阶段工作流 OK        |          | 三阶段 + 知识感知            |
| checkpoint OK          |  =====>  | checkpoint + 知识注入        |
| 多宿主 OK              |          | 多宿主 + CR 集成             |
| 知识库 V2 OK           |          | 知识库 V3 (图谱增强)         |
+------------------------+          +------------------------------+
```

**关键演进：**
- KB V2 → V3：从层级化 markdown 升级为 markdown + 图谱混合资产
- Blueprint Enhancer 架构：插件化扩展知识源（graphify 是第一个 enhancer）
- Phase 4a：CrossReview advisory 模式集成（develop 完成后触发）
- ADR-017：Action Schema Boundary 作为 Sopify P1 架构约束，避免风险打断依赖用户话术白名单；生态层只记录其对 Protocol Step 3、Phase 0.1 和未来 Phase 4b bridge proposal 的约束
- ADR-018：Legacy Surface Retirement 作为 Sopify P1 治理约束；runtime / host cleanup 由 Sopify 总纲负责，生态总纲只追踪跨项目依赖和优先级

### 主线 B：CrossReview 发布与集成

```
+------------------------+          +------------------------------+
| 当前态 (v0-alpha)      |          | 目标态 (v1.0)                |
|                        |          |                              |
| 管线完整 OK            |          | 多制品质量门禁               |
| code_diff only         |  =====>  | code_diff + design_doc       |
| release gate 9/9 OK    |          | + Sopify plugin              |
| v0-05/06/07 pending    |          | + GitHub Action + MCP        |
+------------------------+          +------------------------------+
```

**关键里程碑：**
- v0-04a：unclear_rate 修复 ✅ → v0-04b：全量 release gate 重跑 ✅
- v0-05/06：human-readable + one-stop verify
- v0-07：v0 发布就绪判定 → Phase 2 解锁

### 主线 C：知识工程落地

```
+------------------------+          +------------------------------+
| 当前态 (研究态)        |          | 目标态 (生产态)              |
|                        |          |                              |
| 研究文档完整 OK        |          | blueprint/knowledge/ 层       |
| graphify 可用 OK       |  =====>  | 图谱自动生成+增量更新        |
| BlueprintEnhancer      |          | Skills 注入 AI 上下文        |
|   设计完成, 0% 实现    |          | 规则沉淀 + 反馈闭环          |
+------------------------+          +------------------------------+
```

---

## 3. 整合架构设计

### 3.1 统一知识资产层

**当前问题：** 知识散落在三处，未形成统一资产

| 来源 | 存储位置 | 知识类型 |
|------|---------|---------|
| Sopify KB | `.sopify-skills/blueprint/` | 项目决策、架构设计、任务记录 |
| graphify | `graphify-out/` | 代码结构图谱、模块关系 |
| helloagents | `.helloagents/` | 编码规范、模块文档 |

**统一方案：** 将 graphify 和 helloagents 产出统一归入 blueprint/knowledge/

```
.sopify-skills/                     # Sopify 工作流 + 知识资产（统一）
├── blueprint/                      # 项目级长期知识
│   ├── README.md                   # 索引页（人工策展）
│   ├── background.md               # 项目背景（人工策展）
│   ├── design.md                   # 架构设计（人工策展）
│   ├── tasks.md                    # 长期任务（人工策展）
│   └── knowledge/                  # 机器派生知识（可重建）
│       ├── graph.json              # 项目语义图谱（graphify 生成）
│       ├── modules.md              # 模块职责与关系（图谱 + 推断）
│       ├── flows.md                # 核心业务流程（图谱 + spec）
│       ├── rules/                  # 结构化缺陷规则
│       │   └── {category}.json     # Bad/Good Case
│       └── adr/                    # 架构决策索引
├── plan/                           # 活跃方案包
├── history/                        # 归档
├── state/                          # 运行态（gitignore）
└── user/                           # 用户偏好
```

**设计原则：** blueprint/ 根文件 = 人工策展、不可重建；blueprint/knowledge/ = 机器派生、可随时重建。Sopify 运行时无需修改即可读取。

### 3.2 CrossReview 集成路径

**Phase 4a（Advisory 模式）— CR v0 release gate 通过后解锁：**

```yaml
# .agents/skills/cross-review/skill.yaml
name: cross-review
mode: advisory
trigger: post_develop
default_flow: pack -> render-prompt -> host isolated review -> ingest --format human
fallback_flow: verify --diff --format human  # standalone only; requires reviewer config / API key
```

- Sopify develop 完成后，LLM 自主决定是否调用
- 默认走 host-integrated 路径，由宿主 fresh / isolated review context 执行 canonical prompt
- 显示 findings，用户决定是否接受
- 不阻断工作流，纯建议性质

**Phase 4b（Runtime 模式）— 冻结项，满足启动条件后重新立项：**

> 当前不进入 0-6 个月承诺；即使 Sopify Phase 3 就绪，也需 Phase 4a dogfood 证明 advisory 不够用后再重新评审。

```
develop → 代码完成
    → bridge.py 调用 verify
    → verdict → checkpoint proposal
    → Core 验证并物化决策 checkpoint
    → 修改 → 重新审查（≤2 轮）
    → finalize
```

- 深度集成 Sopify checkpoint 机制
- findings 自动回流知识库

### 3.3 知识工程集成路径

**BlueprintEnhancer 插件架构（知识工程 P2，不阻塞 CR/Sopify 主线）：**

```python
class BlueprintEnhancer(ABC):
    name: ClassVar[str]
    
    @abstractmethod
    def is_available(self) -> bool: ...
    
    @abstractmethod
    def generate(self, workspace: Path) -> EnhancerResult: ...
    
    @abstractmethod
    def update(self, workspace: Path) -> EnhancerResult: ...
    
    @abstractmethod
    def render_auto_sections(self) -> dict[str, str]: ...
```

**GraphifyEnhancer（知识工程 P2 首个实现；区别于 Sopify Phase 5 的 Graphify Advisory Skill）：**
- `is_available()`: pkg:graphifyy → pkg:graphify → import:graphify 链式检测
- `generate()`: 全量图谱 — File → AST → extract → graph → cluster → label → suggest
- `update()`: 增量更新 — SHA256 缓存，只处理变更文件
- `render_auto_sections()`: 自动注入 blueprint 文件的图谱片段

---

## 4. 关键技术决策

### D1：知识资产分层策略

| 层级 | 资产 | 生成方式 | 消费方 |
|------|------|---------|--------|
| L0 | `blueprint/knowledge/graph.json` | graphify 自动生成 | Sopify design/develop Skills |
| L1 | `.sopify-skills/blueprint/` | 人工 + AI 协作 | Sopify 全阶段 |
| L2 | ReviewPack context_files | 从 L0+L1 打包 | CrossReview Reviewer |
| L3 | `user/feedback.jsonl` | 开发者修正记录 | 知识自进化管线 |

### D2：确定性 vs LLM 边界

**设计原则：** 尽可能多的环节用确定性规则，LLM 只用在不可替代处

| 环节 | Sopify | CrossReview |
|------|--------|-------------|
| 路由/分流 | 规则化（复杂度判定） | 规则化（budget guard） |
| 核心推理 | LLM（需求分析、方案设计） | LLM（**仅** Reviewer） |
| 结果校验 | 规则化（checkpoint 合约） | 规则化（Normalizer + Adjudicator） |
| 知识生成 | 混合（graphify AST + LLM 语义） | N/A |

### D3：宿主中立性

**设计原则：** 核心逻辑与宿主解耦

```
核心层 (Python runtime)
    ↓ 合约 (JSON/YAML)
提示层 (Skills/SKILL.md)
    ↓ 同步脚本
宿主适配 (Claude / Codex ✅ 深度验证；QCoder / Copilot 📋 待调研；Trae CN 🔴 Sunset)
```

- 核心运行时用 Python，不依赖任何宿主特性
- 提示层用 SKILL.md markdown，宿主无关
- 宿主适配层做最后一公里映射（工具名映射、输出格式等）

### D4：渐进式集成策略

**设计原则：** 每个阶段独立可交付，不依赖后续阶段

| 阶段 | 独立交付物 | 依赖关系 |
|------|-----------|---------|
| **Protocol Step 1** | `.sopify-skills/` 协议规范文档（plan/state/lifecycle/SKILL.md schema） | 无；P1，不抢 P0 |
| CR v0 release | CLI 工具，独立可用 | 无 |
| Sopify Phase 4a | advisory skill + Convention 模式首次验证 | 依赖 CR v0 |
| Sopify ADR-017 | action schema / side_effect / validator 边界 | 不阻塞 CR v0；约束 Protocol Step 3、Phase 0.1、未来 Phase 4b |
| Protocol Step 2 | Validator CLI (check/doctor/archive)，信号驱动 | Step 1 + 需求信号（含 Phase 4a 验证数据） |
| 知识工程 P2 | blueprint/knowledge/ 资产，独立可用，不阻塞 CR/Sopify | 无 |
| 知识工程 P3 | query skill，增强 Sopify | 依赖 P2 |
| CR + 知识 | context_files 含业务知识 | 依赖 P2 + CR v0 |

---

## 5. 风险与缓解

### 高风险

| 风险 | 影响 | 缓解措施 |
|------|------|---------|
| **协议复杂度膨胀** | Sopify runtime gate 已很厚重，继续叠加可能导致"协议比业务代码复杂" | 定期协议瘦身审计；每季度评估 gate 条件是否可合并 |
| **用户话术白名单膨胀** | 风险打断持续追逐用户表达习惯，维护成本高且无法穷举 | ADR-017：LLM 只提议结构化 action，Core/Validator 基于机器事实、side_effect、风险策略授权 |
| **遗留 surface 长期残留** | runtime/host 改造后旧入口、旧测试、旧文档未清理，导致"看似兼容、实际无人维护"的维护黑洞 | ADR-018：改造时必须声明退出路径（frozen / sunset / removed）；frozen 不计入 release gate |
| **BlueprintEnhancer 延期** | 阻塞知识工程后续任务（已降 P2，不阻塞 CR/Sopify 主线） | 设定硬截止日期；必要时先用最小 shim 绕过 |
| **CR 发布就绪链未完成** | release gate 已通过，但 PyPI 与 Sopify Phase 4a 尚不能解锁 | 完成 v0-05 human-readable、v0-06 one-stop verify、v0-07 发布就绪判定 |

### 中风险

| 风险 | 影响 | 缓解措施 |
|------|------|---------|
| graphify 更新频率 | 依赖外部项目，Python <3.13 限制 | 锁定版本 + 建立 fork 应急计划 |
| 知识图谱噪音 | LLM 语义提取可能引入错误关系 | confidence 标签 + 人工校验抽检 |
| 宿主 surface 膨胀 | 活跃、调研、sunset 宿主混在同一支持矩阵中，导致提示层/测试/脚本长期残留 | ADR-018：Sopify 总纲负责 host cleanup；生态总纲只追踪完成状态 |

### 低风险

| 风险 | 影响 | 缓解措施 |
|------|------|---------|
| 规则沉淀冷启动 | Phase 3 无初始数据 | 先手动沉淀 20-50 条高频规则 |
| helloagents 目录冲突 | 与 .sopify-skills/ 平行存在 | 逐步迁移，不强制统一 |

---

## 6. 成功标准

### 短期（2026 H1 剩余）

- [x] CR v0 release gate 9/9 通过
- [ ] CR v0 正式发布 + Sopify Phase 4a advisory 端到端跑通
- [ ] Protocol Step 1 文档完成（Sopify 协议规范独立可读）

### 中期（2026 H2）

- [ ] BlueprintEnhancer ABC 实现 + graphify enhancer MVP（P2，不阻塞短期主线）
- [ ] `blueprint/knowledge/graph.json` 在至少 1 个真实项目生成
- [ ] Sopify Phase 4a (advisory CR) 跑通端到端
- [ ] Phase 4a Convention 模式验证报告完成，Protocol Step 2/3 激活决策基于验证数据
- [ ] knowledge-graph-query Skill 在 design/develop 阶段自动注入上下文
- [ ] CR 支持 host-integrated 模式（宿主隔离执行，零 API 成本）

### 长期（2027）

- [ ] 三环飞轮形成：生产 → 验证 → 知识 → 生产
- [ ] 规则沉淀 ≥ 50 条 + 反馈闭环初步运转
- [ ] 知识注入后 CR 可做业务逻辑评审（解锁"跳过业务逻辑"限制）
