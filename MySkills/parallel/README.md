# 并行技能包 (Parallel Skills)

> 本技能包负责子智能体并行编排。设计为**并行优先**，不支持的宿主自动降级为顺序模式。

## 技能列表

| 技能 | 用途 | 触发时机 |
|------|------|---------|
| [team-review](team-review/) | 代码实现后运行 code-review + design-review + architecture-review | 每次 M/L 任务完成后 |
| [team-parallel-analyze](team-parallel-analyze/) | L 级前期或跨多文档时，并行 spawn 只读子智能体各自分析文档后汇总 | 需同时分析 ≥3 份不相关文档时 |
| [team-project-bootstrap](team-project-bootstrap/) | 项目启动时并行 spawn 3 个子智能体 | 项目代码目录为空且用户确认启动时 |
| [team-parallel-implement](team-parallel-implement/) | 日常开发中 ≥3 个互不依赖的模块并行编码 | M/L 任务中，主智能体确认依赖关系后 |

## 双模式执行

| 宿主 | 模式 | 行为 |
|------|------|------|
| Claude Code（支持自定义 agent type） | **并行** | 同时 spawn 3 个独立子智能体 |
| OpenCode（仅 explore/general agent type） | **降级顺序** | 主上下文内顺序切换角色执行 3 轮审查 |

**两种模式共同要求：**
- 强制读取 `Docs/Current/project-fingerprint.md` 验证项目身份
- 强制从 `MySkills/agents/` 加载 HR 产出的角色定义
- 禁止"模拟"审查或跳过角色注入步骤
- 输出报告格式一致

详见 `AGENTS.md §7.7` 和 `team-review/SKILL.md`。

## 使用原则

- `team-review`、`team-parallel-analyze`：子智能体只读不写，主智能体负责汇总
- `team-project-bootstrap`：子智能体只读返回方案与代码建议，主智能体整合后统一写入
- `team-parallel-implement`：子智能体仅可写入分配给自己的文件，主智能体负责集成审查与验证
- 并行 spawn 前确认各子智能体的输入互不依赖
- 若子智能体被 BLOCKED，立即暴露给用户

## 自定义 Agent 优先

在 spawn 每个子智能体前，主智能体必须执行以下查表步骤：

1. 读取 `MySkills/agents/{agent-name}.md`
2. 如文件存在：将全文作为该子智能体的角色 prompt 注入，并输出 `✓ 使用自定义定义：MySkills/agents/{agent-name}.md`
3. 如文件不存在：使用本技能 SKILL.md 中的默认职能描述，并输出 `⚠ 未找到自定义 agent 定义，使用默认角色描述`

禁止静默回退。注册约定详见 `MySkills/agents/README.md`。

## 专项约束前置

在 spawn 每个子智能体前，主智能体还必须完成专项约束查表与注入：

1. 读取 `MySkills/project-constraints/README.md`
2. 按本轮任务目标、涉及模块、文件范围与角色，决定是否装载已注册的专项约束包
3. 在 spawn 前输出 `✓ 使用专项约束：...` 或 `ℹ 当前无专项约束包生效`
4. 将已匹配的专项约束作为主智能体统一注入的上下文提供给子智能体

禁止让子智能体自行扫描 `MySkills/project-constraints/` 猜测应该加载哪些规则。
`team-review`、`team-parallel-analyze`、`team-project-bootstrap`、`team-parallel-implement` 消费的是主智能体**已经注入**的专项约束，而不是各自重新发现的目录内容。

## 并行任务协议

1. 所有独立的 Task 调用在等任何结果之前发出
2. 收集所有结果后才进入依赖阶段
3. 若任何智能体 BLOCKED，主智能体立即停止并报告
4. 部分完成时出部分报告，不静默跳过

## Prompt 前缀共享

并行 spawn 的多个子智能体应最大化前缀重合以利用 LLM 缓存（按 token 前缀匹配，非按对话）：

- **共享前缀段**（固定）：AGENTS.md、技能 SKILL.md、项目约定文档 → 所有子智能体完全一致
- **差异段**（固定）：各子智能体的角色描述 / 自定义 agent 定义
- **动态段**（变化）：本次任务上下文、改动文件列表、具体问题 → 放在 prompt 最末尾

多个子智能体共享相同前缀段时，该段跨子智能体命中缓存。详见 `AGENTS.md` §7.6。
