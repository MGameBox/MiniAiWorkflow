# Claude Code 游戏开发协作框架

> 一套可迁移、渐进式、基于 Claude Code 子智能体的游戏开发 AI 协作工作流。

## 这是什么

这个框架解决一个核心问题：**AI 协作项目时，上下文容易被污染、多维度审查串行等待、项目状态分散不可追踪**。

它把 Claude Code 的子智能体（Subagent）上下文隔离和并行能力，与一套轻量但完整的文档/技能/规则体系结合，形成可复制的游戏开发协作工作流。

**设计原则**：

- **可迁移**：不绑定具体引擎或项目结构，任何游戏项目都可以粘贴初始化
- **渐进式**：从最小骨架开始，按需扩展，不一次性引入过重流程
- **上下文隔离**：子智能体只读所需文档，避免主会话被无关信息污染
- **并行提效**：代码审查、设计审查、架构审查三个维度同时运行
- **状态单一源**：`session-active.md` 替代分散的状态文件

---

## 核心特性

| 特性 | 说明 |
|------|------|
| **S/M/L 任务分级** | 小/中/大任务不同流程，小任务直接执行，大任务必须出方案 |
| **迭代日志强制闭环** | 每个迭代必须写独立日志文件，禁止用一句话替代 |
| **team-review 双模式审查** | M/L 任务完成后审查（代码/设计/架构）。并行优先（Claude Code），降级顺序兜底（OpenCode）。两种模式都强制从 `MySkills/agents/` 加载 HR 角色定义 |
| **混合触发模式** | 默认提示 + 可选自动 + 关键词兜底（如 `/mywork-review`，可自定义 `/zmc`） |
| **模型分层** | Haiku 查状态、Sonnet 写代码、Opus 做架构，按任务复杂度选模型 |
| **HR 技能招聘子智能体** | 引导用户创建带角色约束的个性化 agent 设定 |
| **专项约束接线** | 通过 `/mywork-constraints` 把行业规范、项目命名约定、模块规则注册为可装载约束包 |
| **自动初始化 .claude/** | 通过 `claude-setup` 技能在初始化时自动释放 hooks + 规则 |
| **session-active 单一状态源** | 替代分散的"当前阶段/当前重点/下一步"文件 |

---

## 框架结构

粘贴到项目根目录后，结构如下：

```
项目根目录/
├── AGENTS.md                           # 核心协作规则（任务分级、流程、边界）
├── AI协作启动工作流-纯净优化单文件版.md   # AI 首次接手的启动入口
├── AI协作框架-使用说明.md               # 本文件：框架总览与使用指南
│
├── Docs/                               # 文档系统
│   ├── README.md                       # 文档系统导航
│   ├── Current/
│   │   ├── session-active.md           # 单一状态源（当前任务、进度、决策、活跃文件）
│   │   └── project-fingerprint.md      # 项目身份文件（审查前强制读取）
│   ├── Architecture/                   # 架构文档
│   ├── Plans/                          # 计划文档（YYYY-MM-DD-前缀）
│   ├── IterationLogs/                  # 迭代日志（YYYY-MM-DD-前缀，独立文件）
│   ├── Templates/                      # 文档模板
│   └── Archive/                        # 归档系统（阶段切换/计划完成/日志堆积时触发）
│       ├── Plans/                      # 已归档计划
│       ├── IterationLogs/              # 已归档迭代日志
│       └── Current/                    # 历史阶段状态存档
│
├── MySkills/                           # 本地 AI 技能包
│   ├── README.md                       # 技能导航
│   ├── agents/                         # 自定义 Agent 注册目录
│   │   ├── README.md                   # 注册约定（由 hr 产出，由并行技能和降级模式消费）
│   │   ├── tech-lead.md                # 技术负责人
│   │   ├── game-designer.md            # 游戏设计师
│   │   ├── qa.md                       # QA
│   │   ├── python-dev.md               # Python 服务端开发
│   │   ├── godot-dev.md                # Godot 客户端开发
│   │   └── doc-engineer.md             # 文档工程师
│   ├── project-constraints/            # 项目专项约束注册目录
│   │   └── README.md
│   ├── parallel/                       # 并行子智能体技能
│   │   ├── README.md
│   │   ├── team-review/                # 并行审查（代码+设计+架构；含降级顺序模式）
│   │   │   └── SKILL.md
│   │   ├── team-parallel-analyze/      # 并行文档分析
│   │   │   └── SKILL.md
│   │   ├── team-project-bootstrap/     # 项目启动并行
│   │   │   └── SKILL.md
│   │   └── team-parallel-implement/    # 日常开发并行编码
│   │       └── SKILL.md
│   ├── workflow/
│   │   ├── claude-setup/               # .claude/ 配置自动释放
│   │   │   └── SKILL.md
│   │   ├── hr/                         # 子智能体招聘与定制
│   │   │   ├── SKILL.md
│   │   │   ├── templates/
│   │   │   │   └── agent-template.md
│   │   │   └── agents/
│   │   │       ├── tech-lead.md
│   │   │       ├── designer.md
│   │   │       ├── qa.md
│   │   │       ├── pm.md
│   │   │       └── doc-engineer.md
│   │   └── constraints/                # 专项约束接线
│   │       └── SKILL.md
│   ├── my-docs-skill/                  # 文档系统增强（Obsidian 友好等）
│   │   └── SKILL.md
│   └── my-super-powers/                # 流程增强（计划、评审、验证等）
│       └── SKILL.md
│
└── .claude/                            # Claude Code 配置（初始化时自动创建）
    ├── settings.json                   # Hook 注册 + 权限规则
    ├── rules/                          # 路径级规则
    │   ├── docs-plans.md               # 计划文档命名规则
    │   └── docs-logs.md                # 迭代日志独立文件规则
    └── hooks/                          # 自动化脚本
        ├── hook_runner.py              # 默认 hook 入口（跨平台）
        ├── session-start.sh            # 辅助 shell 版本：显示状态摘要
        ├── detect-gaps.sh              # 辅助 shell 版本：检测项目缺口
        ├── pre-compact.sh              # 辅助 shell 版本：压缩前保存进度
        └── post-compact.sh             # 辅助 shell 版本：压缩后提示恢复上下文
```

---

## 快速开始

### 第一步：粘贴框架文件

把以下文件/目录复制到新项目的根目录：

- `AGENTS.md`
- `AI协作启动工作流-纯净优化单文件版.md`
- `MySkills/`（完整技能包）

> `Docs/` 与 `.claude/` **都不要复制**，它们由初始化流程自动生成。

### 第二步：初始化项目

在 Claude Code 中打开新项目后，AI 会读取 `AI协作启动工作流-纯净优化单文件版.md`，自动执行以下动作：

1. 扫描项目结构
2. 初始化 `Docs/` 最小骨架（如缺失）
3. 检查 `MySkills/` 并读取匹配技能
4. **询问是否启用 Claude Code 自动增强**
5. 用户确认后，读取 `MySkills/workflow/claude-setup/SKILL.md` 并自动创建 `.claude/` 目录及所有配置文件

### 第三步：开始协作

初始化完成后，AI 会展示 `Docs/Current/session-active.md` 的状态摘要，等待用户下达任务。

---

## 组件详解

### AGENTS.md — 核心协作规则

这是整个框架的"宪法"。AI 每次接手任务时都会读取它。包含任务分级（S/M/L）、启动顺序（含项目身份验证）、方案门禁、执行后审查（team-review 双模式）、子智能体与并行（含降级兜底）、文档规则（含归档触发）、完成定义和通用编码规范。

完整规则定义见 `AGENTS.md`。

### Docs/ — 文档系统

**session-active.md**（最重要）

单一状态源，session 中断后从此恢复上下文。包含：

```markdown
<!-- STATUS -->
Epic: xxx
Feature: xxx
Task: xxx
<!-- /STATUS -->

## 当前任务
## 进度检查
## 关键决策
## 活跃文件
## 待确认
## 下一步入口
```

**可配置项**（在"关键决策"表格中设置）：

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `auto-team-review` | `false` | `true` 时 M/L 任务完成后自动运行审查 |
| `review-trigger-keyword` | `/mywork-review` | 主动触发审查的关键词，可自定义如 `/zmc` |

**Plans/** 和 **IterationLogs/**

- 计划文档：文件名必须带 `YYYY-MM-DD-` 前缀，必须包含迭代总览
- 迭代日志：每个已完成迭代写独立文件，禁止用一句话替代

**project-fingerprint.md** — 项目身份文件

任何审查/分析/子智能体任务启动前强制读取。包含项目名称、绝对根路径和技术栈签名。路径不匹配时立即停止，防止引用旧项目上下文。

**Archive/** — 归档系统

阶段切换、计划完成、日志堆积（≥10条）或计划废弃时触发归档。归档内容迁移到 `Docs/Archive/` 对应子目录，并在索引中记录归档时间和原因。规则详见 `AGENTS.md §5.4`。

### MySkills/ — 本地技能包

技能是 AI 的"外挂"，在特定场景下激活特定流程。

**parallel/ — 并行子智能体**

- **team-review**：M/L 任务完成后审查代码/设计/架构三个维度。支持双模式：Claude Code 等支持自定义 agent type 的环境使用并行 spawn，OpenCode 等仅支持 explore/general 的环境自动降级为顺序角色切换。两种模式都强制从 `MySkills/agents/` 加载 HR 产出的角色定义，禁止跳过角色注入。
- **team-parallel-analyze**：L 级前期或跨多文档时，并行 spawn 多个只读分析子智能体，按切片返回结构化摘要，再由主智能体统一汇总风险点、边界条件和待确认问题。
- **team-project-bootstrap**：项目启动时并行 spawn 3 个只读实现子智能体，覆盖 `engine-setup`、`core-render`、`core-gameplay` 三类职责；子智能体只返回方案、接口和代码建议，由主智能体整合后统一写入。
- **team-parallel-implement**：日常开发中，按文件白名单并行 spawn 多个实现子智能体；仅适用于 ≥3 个互不依赖模块且接口契约已先定义的场景，子智能体只能写入分配给自己的文件，主智能体负责集成审查与验证。

**workflow/hr/ — 子智能体招聘**

引导用户创建个性化子智能体，产出到 `MySkills/agents/`。内置 5 个模板（tech-lead / designer / qa / pm / doc-engineer）。使用方式：告诉 AI "用 hr 技能招聘一个 <引擎> 项目的 <角色>"。详见 `MySkills/workflow/hr/SKILL.md`。

**MySkills/agents/ — 自定义 Agent 注册目录**

由 hr 产出，由 team-review 等消费。并行模式 spawn 注入，降级模式顺序加载。详见 `MySkills/agents/README.md`。

**workflow/constraints/ — 专项约束接线**

注册行业规范/命名约定/模块规则为可装载约束包。详见 `MySkills/workflow/constraints/SKILL.md`。

- 触发命令：`/mywork-constraints`
- 固定索引：`MySkills/project-constraints/README.md`
- 装载原则：主智能体先读取固定索引，按任务匹配并显式回显，再把结果注入给 HR、team-review、team-parallel-analyze、team-project-bootstrap、team-parallel-implement 消费
- 边界：子智能体不得自行扫描 `MySkills/project-constraints/` 猜测规则

如果当前没有任何已注册的专项约束包，`session-active.md` 中的 `ACTIVE-CONSTRAINTS` 区块也必须保留“当前无生效的专项约束包”默认态，而不是留空。

**workflow/claude-setup/ — .claude/ 自动释放**

这不是让用户手动复制的配置，而是在初始化时由 AI 读取并自动创建的。

它会根据项目情况创建：
- `.claude/settings.json` — Hook 注册、权限规则
- `.claude/rules/` — 路径级文档规则
- `.claude/hooks/` — 自动化脚本（默认通过 `hook_runner.py` 执行；保留辅助 shell 版本）

### .claude/ — Claude Code 配置（自动创建）

**Hooks**：

默认入口：`.claude/hooks/hook_runner.py`

说明：

- `settings.json` 默认调用 Python hook runner，减少 Windows / 无 `bash` 环境下的阻塞。
- `session-start.sh`、`detect-gaps.sh`、`pre-compact.sh`、`post-compact.sh` 保留为辅助 shell 实现，而不是唯一执行入口。

| Hook | 触发时机 | 作用 |
|------|----------|------|
| `session-start` | 打开新 session | 显示 `session-active.md` 摘要 |
| `detect-gaps` | 打开新 session | 检测项目缺口（如代码存在但无架构文档） |
| `pre-compact` | Session 压缩前 | 记录时间戳到 `session-active.md` |
| `post-compact` | Session 压缩后 | 提示读取 `session-active.md` 恢复上下文 |

**Rules**（路径级约束）：

- `docs-plans.md`：计划文档必须带 `YYYY-MM-DD-` 前缀，必须包含迭代总览
- `docs-logs.md`：迭代日志必须独立文件，禁止用一句话替代

---

## 典型工作流示例

### 场景 1：修复一个 UI Bug（S 级任务）

```
用户：修复开始按钮点击没反应的问题
AI ：（读取 AGENTS.md → 判定为 S 级 → 直接执行）
    1. 读取相关代码
    2. 定位问题
    3. 修复
    4. 验证（运行/编译检查）
    5. 写迭代日志到 Docs/IterationLogs/YYYY-MM-DD-修复开始按钮.md
    6. 完成
```

### 场景 2：接入新的战斗系统（M 级任务）

```
用户：我要接入一个连击系统
AI ：（读取 AGENTS.md → 判定为 M 级 → 先出方案）
    1. 确认需求边界
    2. 给出最小方案 + 迭代计划
    3. 等待用户确认
    4. （确认后）执行第 1 个迭代
    5. 验证
    6. 写迭代日志
    7. 提示用户确认是否继续
    8. （用户确认后）执行第 2 个迭代
     9. ...
     10. 全部迭代完成后 → 提示是否运行 team-review
     11. （用户确认 /mywork-review）审查启动：
         - M 级：code-review + design-review（未跨模块不跑 architecture）
         - L 级：完整三轮（code + design + architecture）
         - 强制加载 MySkills/agents/ 角色定义
     12. 汇总审查意见 → 用户确认后修改 → 完成
```

### 场景 3：启动新 Unity 项目（L 级任务）

```
用户：我要启动 Unity 正式版
AI ：（读取 AGENTS.md → 判定为 L 级 → 头脑风暴 → 方案）
    1. 给出 3 个启动方案选项（轻量/标准/完整）
    2. 用户选择后写入 Docs/Plans/YYYY-MM-DD-Unity启动计划.md
    3. 等待用户确认计划
    4. （确认后）读取 parallel/team-project-bootstrap/SKILL.md
    5. 并行 spawn engine-setup + core-render + core-gameplay
    6. 收集 3 份实现结果 → 整合写入统一代码结构
    7. 验证
    8. 写迭代日志
    9. 进入下一迭代...
```

### 场景 4：使用 HR 技能招聘子智能体

```
用户：用 hr 帮我创建一个 Godot 项目的 tech-lead
AI ：（读取 MySkills/workflow/hr/SKILL.md）
    1. 展示预置 5 个通用模板
    2. 用户选择 tech-lead
    3. 询问个性化需求（技术栈、审查风格、输出格式等）
    4. 用户确认后生成个性化 agent 设定
    5. 输出到 MySkills/agents/godot-tech-lead.md（或用户指定位置）
    6. 后续 team-review spawn 时优先使用个性化设定
```

---

## 配置参考

### session-active.md 关键字段

```markdown
<!-- STATUS -->
Epic: 当前大目标
Feature: 当前功能线
Task: 当前具体任务
<!-- /STATUS -->

<!-- REVIEW-CONFIG -->
auto-team-review: false
review-trigger-keyword: /mywork-review
review-trigger-fallback: prompt-user
<!-- /REVIEW-CONFIG -->

<!-- ACTIVE-CONSTRAINTS -->
当前无生效的专项约束包
<!-- /ACTIVE-CONSTRAINTS -->

## 关键决策
| 决策 | 状态 | 影响 |
| auto-team-review | true/false | 是否自动运行审查 |
| review-trigger-keyword | /mywork-review | 触发关键词，可改 /zmc 等 |
```

说明：

- `REVIEW-CONFIG` 是 machine-readable 审查配置源。
- `ACTIVE-CONSTRAINTS` 是专项约束的 machine-readable 可见性区块，用于显示当前已加载或默认启用的约束包。
- 默认消费脚本：`.claude/hooks/review-gate.sh`
- Windows / 无 bash 的本地降级路径：手动执行 `.claude/hooks/review-gate.cmd`

### team-review 触发方式

| 方式 | 条件 | 行为 |
|------|------|------|
| 默认提示 | `auto-team-review: false`（默认） | M/L 完成后询问用户是否审查 |
| 自动运行 | `auto-team-review: true` | M/L 完成后直接并行 spawn 审查 |
| 关键词触发 | 用户输入关键词 | 随时触发，不受任务级别限制 |

### 模型选择参考

| 模型 | 何时使用 | 何时不用 |
|------|----------|----------|
| Haiku | 查状态、扫文件列表、格式化 | 写代码、做方案 |
| Sonnet | 默认；写代码、单系统设计、调试 | 跨 5+ 文档的综合分析 |
| Opus | 架构方案、L 级计划、跨模块重构 | 小修复、单文件改动 |

---

## 自定义与扩展

### 自定义审查触发关键词

优先修改 `Docs/Current/session-active.md` 中的 `REVIEW-CONFIG` 配置块；表格说明用于人读，不是最终执行源：

```markdown
<!-- REVIEW-CONFIG -->
auto-team-review: false
review-trigger-keyword: /zmc
review-trigger-fallback: prompt-user
<!-- /REVIEW-CONFIG -->
```

以后输入 `/zmc` 就会触发 team-review。

### 自定义子智能体角色

使用 `MySkills/workflow/hr/SKILL.md`：

1. 告诉 AI "我要用 hr 技能"
2. 选择模板（tech-lead / designer / qa / pm / doc-engineer）
3. 描述项目需求（引擎类型、团队规模、偏好风格）
4. AI 生成个性化设定，你可以继续修改
5. 确认后输出到指定位置

### 接入行业规范 / 项目命名约定 / 模块规则

使用 `MySkills/workflow/constraints/SKILL.md`：

1. 告诉 AI "我要用 /mywork-constraints 接入一份 Unity 规范" 或 "把这份项目命名约定接进工作流"
2. AI 读取 `MySkills/project-constraints/README.md`，确认当前是否已有已注册约束包
3. AI 将你的材料整理成专项约束包，并登记到 `MySkills/project-constraints/`
4. 后续主智能体会在执行、并行或 HR 前先匹配这些约束，并把当前结果写到 `session-active.md` 的 `ACTIVE-CONSTRAINTS` 区块

### 添加新的路径规则

在 `.claude/rules/` 下新建 `.md` 文件，顶部写：

```markdown
---
appliesTo: "路径模式/**"
---

# 规则标题

约束内容...
```

### 添加新的 Hook

1. 在 `.claude/hooks/` 下写脚本
2. 在 `.claude/settings.json` 中注册

### 运行最小一致性检查

扩展 slash 命令、并行模式、初始化规则或默认值后，建议运行：

```text
python .claude/hooks/hook_runner.py workflow-consistency
```

它会检查当前最容易回漂的几类约束：命令字、并行技能目录、消费范围，以及 `Docs/` / `.claude/` 的自动生成语义。

如果你已经启用了专项约束层，它还会检查：`workflow/constraints` 是否落地、`MySkills/project-constraints/README.md` 是否存在、`ACTIVE-CONSTRAINTS` 是否已暴露，以及使用说明是否仍能找到 `/mywork-constraints` 入口。

稳定同步面与维护基线以 `AGENTS.md`、`MySkills/README.md` 以及对应技能 `SKILL.md` 为准。

---

## 注意事项

1. **不要手动复制或预创建 `Docs/` / `.claude/`**：两者都由初始化流程按当前项目状态自动生成；用户只需要提供框架入口文件和 `MySkills/`
2. **审查范围按任务级别区分**：M 级默认只跑 code-review + design-review（跨模块时追加 architecture）；L 级完整三轮
3. **迭代日志必须独立文件**：禁止在计划文档里补一句"第 N 迭代已完成"来替代
4. **计划写完后必须停下等待确认**：这是防止 AI 自作主张推进的关键约束
5. **状态只有 session-active.md 这一个地方**：不要同时维护多个状态文件
6. **引擎相关规则在 AGENTS.md 中是语义级的**：不绑定具体路径，根据文件内容判断是否适用
7. **审查前强制验证项目身份**：team-review 启动时必须先读取 `Docs/Current/project-fingerprint.md` 确认项目根目录一致
8. **适时归档**：阶段切换或日志堆积时，AI 会提示归档到 `Docs/Archive/`

---

## 迁移清单

把本框架迁移到新项目时，复制这些文件即可：

```
AGENTS.md
AI协作启动工作流-纯净优化单文件版.md
AI协作框架-使用说明.md
MySkills/（整个目录）
```

> `Docs/` 与 `.claude/` 都由初始化流程自动生成；不要复制它们，也不要复制 `node_modules/`、`bin/` 等项目构建产物。

---

*框架版本：v1.0 | 最后更新：2026-05-24*
