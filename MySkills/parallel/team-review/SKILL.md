---
name: team-review
description: "实现完成后可运行完整 team-review；跨模块计划成稿前可单独复用 architecture-review 角度做预审。支持并行（Claude Code）和降级顺序（OpenCode）双模式。"
tools: Read, Agent
model: sonnet
---

# team-review 技能

> 硬性触发条件以 `AGENTS.md` 为准。本技能只补充审查动作如何执行，不单独改写任务分级与门禁。

## 触发时机

审查范围按任务级别区分：

- **M 级任务**：默认运行 code-review + design-review。仅当改动涉及跨模块、目录迁移或新基础设施时，追加 architecture-review。
- **L 级任务**：运行完整三轮（code-review + design-review + architecture-review）。
- 跨模块计划在计划成稿前，可单独运行 architecture-review 角度做预审（不要求同时运行 code-review 和 design-review）。
- `session-active.md` 的 `REVIEW-CONFIG` 中 `auto-team-review: true` 时自动触发。
- 用户输入触发关键词时（默认 `/mywork-review`）主动触发，不受任务级别限制。

## 特例：计划前 architecture-review 预审

- 仅用于跨模块计划成稿前的结构预审。
- 只运行 `architecture-review` 角度，检查模块边界、依赖方向、可维护性与计划收口方式。

---

## 审查角色与 Agent 映射

| 审查角色 | 默认 Agent 文件 | 审查重点 |
|---------|----------------|---------|
| code-review | `MySkills/agents/tech-lead.md` | 正确性、性能、边界、安全 |
| design-review | `MySkills/agents/game-designer.md` | 设计文档一致性、玩家体验、平衡性 |
| architecture-review | `MySkills/agents/tech-lead.md` | 模块边界、依赖方向、可维护性 |

> 若 `MySkills/agents/` 下存在更贴合的角色文件（如 `godot-dev.md`、`python-dev.md`），主智能体可根据改动文件自行匹配，但必须在 spawn 前显式声明所用文件。

---

## Pre-flight 检查清单（所有模式强制）

审查开始前，主智能体必须完成以下检查：

1. **项目身份**：读取 `Docs/Current/project-fingerprint.md`，输出：
   ```
   ✓ 当前项目：CrossGate 复刻（D:\BaiduNetdiskDownload\TwMlBB）
   ```
   路径不匹配立即停止。

2. **能力检测**：列出当前可用的 Agent 工具 agent type，判断是否包含 `code-review`、`design-review`、`architecture-review` 等命名角色。

3. **范围判定**：根据任务级别确定审查范围：
   - M 级：默认 code-review + design-review；跨模块/目录迁移时追加 architecture-review
   - L 级：完整三轮（code-review + design-review + architecture-review）

4. **模式判定**：
   - 可用命名角色 → **并行模式**
   - 仅 `explore` / `general` → **降级顺序模式**

4. **角色定义加载**：按映射表读取对应的 `MySkills/agents/*.md` 文件。

---

## 执行流程

### Step 0: 模式声明（强制）

在开始审查前，主智能体必须输出：

```text
✓ 项目身份：CrossGate 复刻（D:\BaiduNetdiskDownload\TwMlBB）
ⓘ 任务级别：[M] 或 [L]
ⓘ 审查范围：[code+design] 或 [code+design+architecture]
ⓘ 当前模式：[并行] 或 [降级顺序]（原因：仅支持 explore/general agent type）
ⓘ 使用角色定义：
  - code-review: MySkills/agents/tech-lead.md
  - design-review: MySkills/agents/game-designer.md
  - architecture-review: MySkills/agents/tech-lead.md（如适用）
```

---

### Step A: 并行模式（Claude Code 等）

当能力检测确认支持自定义命名 agent type 时使用。

#### A1. 并行 spawn 3 个子智能体

同时发出 3 个 Agent 调用，不串行等待：

```
主智能体
  ├─→ Subagent A: code-review
  │     角色 prompt: 读取 MySkills/agents/tech-lead.md 全文注入
  │     只读：改动文件 + 相关代码
  │     检查：正确性、性能、边界、安全
  │
  ├─→ Subagent B: design-review
  │     角色 prompt: 读取 MySkills/agents/game-designer.md 全文注入
  │     只读：改动文件 + 核心设计文档
  │     检查：与设计文档一致性、玩家体验、平衡性
  │
  └─→ Subagent C: architecture-review
        角色 prompt: 读取 MySkills/agents/tech-lead.md 全文注入
        只读：改动文件 + Architecture/项目约定.md
        检查：模块边界、依赖方向、可维护性
```

#### A2. 主智能体汇总

收集 3 份审查报告，按汇总格式输出。

---

### Step B: 降级顺序模式（OpenCode 等）

当能力检测确认仅支持 `explore` / `general` agent type 时使用。

**禁止在此模式下宣称 spawn 了子智能体、禁止跳过角色注入直接"模拟"。**

#### B1. 顺序执行三轮审查

**每轮必须**：
1. 读取对应 agent 定义文件（`MySkills/agents/{role}.md`）
2. 显式输出 `✓ 使用自定义定义：MySkills/agents/{role}.md`
3. 以该角色 persona 完成审查
4. 产出该角色审查报告
5. 标明 "第 N/3 轮完成"

**执行顺序**：
```
第 1/3 轮: code-review
  → 读取 tech-lead.md → 切换 persona → 审查 → 出报告

第 2/3 轮: design-review
  → 读取 game-designer.md → 切换 persona → 审查 → 出报告

第 3/3 轮: architecture-review
  → 读取 tech-lead.md → 切换 persona → 审查 → 出报告
```

#### B2. 主智能体汇总

三轮完成后，按汇总格式统一输出。

---

## 汇总格式

两种模式共用此格式：

```markdown
## Team Review 汇总

### Code Review
角色: tech-lead (MySkills/agents/tech-lead.md)
- 🔴 阻塞: ...
- 🟡 建议: ...
- 🟢 提示: ...

### Design Review
角色: game-designer (MySkills/agents/game-designer.md)
- 🔴 阻塞: ...
- 🟡 建议: ...

### Architecture Review
角色: tech-lead (MySkills/agents/tech-lead.md)
- 🔴 阻塞: ...
- 🟡 建议: ...

### 冲突点
（如果任意两个审查角度的意见矛盾，显式标注）

### 修改计划
1. ...
2. ...
```

---

## 禁止项

- 子智能体不得使用 Write/Edit 工具
- 子智能体不得修改任何文件
- 不得在无审查的情况下宣称任务完成
- 不得只运行一个审查角度就跳过其他角度
- 禁止在降级模式下"模拟"审查而不加载 HR agent 定义
- 禁止在降级模式下宣称 "spawn 了子智能体"
- 禁止跳过 Step 0 的模式声明
