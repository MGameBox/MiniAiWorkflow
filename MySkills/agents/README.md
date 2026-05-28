# 自定义 Agent 注册目录

> 本目录是项目级自定义子智能体角色定义的**标准注册面**。
> 由 `workflow/hr` 产出，由 `parallel/team-review`、`parallel/team-parallel-analyze`、`parallel/team-project-bootstrap` 与 `parallel/team-parallel-implement` 消费。

## 注册约定

### 目录

```text
MySkills/agents/
├── README.md          ← 本文件（注册约定）
├── tech-lead.md       ← 示例：技术负责人（需通过 /mywork-hr 生成）
├── designer.md        ← 示例：设计师
└── qa.md              ← 示例：QA
```

### 命名规则

- 文件名：`{agent-name}.md`，小写英文，用连字符分隔（如 `tech-lead.md`、`doc-engineer.md`）
- 文件名必须与 `workflow/hr` 预置模板名或用户自定义角色名一致
- 每文件定义一个角色

### 优先级

并行技能（`team-review`、`team-parallel-analyze`、`team-project-bootstrap`、`team-parallel-implement`）在 spawn 子智能体时：

1. **优先读取** `MySkills/agents/{agent-name}.md`，将全文作为子智能体的角色 prompt 注入
2. **如文件不存在或读取失败**，回退到对应技能 SKILL.md 中的默认职能描述
3. **如存在同名冲突**（多个 agent 文件声明同一角色名），取文件名精确匹配者；如仍冲突，取最近修改者并显式警告

### 回退规则

回退时主智能体**必须**在 spawn 前输出：

```text
⚠ 未找到自定义 agent 定义 MySkills/agents/{agent-name}.md，使用默认角色描述。
```

禁止静默回退。

### 冲突处理

| 冲突类型 | 处理方式 |
| --------- | --------- |
| 同名文件（不区分大小写） | 取精确匹配；否则取最近修改者并输出警告 |
| 自定义 agent 与 AGENTS.md 主规则冲突 | **AGENTS.md 优先**；自定义角色不得绕过项目级规则 |
| 自定义 agent 声明了超出当前模式的工具权限 | 并行技能 spawn 时按当前模式降级：`team-review` / `team-parallel-analyze` 只读，`team-project-bootstrap` 只读返回方案，`team-parallel-implement` 仅保留分配文件所需写权限 |

## 消费路径

本目录下的 agent 定义通过两种路径被消费：

### 路径一：并行 spawn（Claude Code 等）

并行技能 spawn 子智能体时，将 agent 文件全文作为角色 prompt 注入到子智能体上下文。

### 路径二：降级顺序切换（OpenCode 等）

当宿主不支持自定义命名 agent type 时，主智能体在自身上下文中顺序切换角色：
1. 每轮开始前读取对应的 agent 定义文件
2. 以该角色的 persona、专长、审查风格和输出格式完成审查
3. 产出该角色审查报告后再加载下一角色
4. 三轮完成后统一汇总

**关键**：降级模式不是"模拟"，而是严格加载 HR 产出的角色定义后执行。禁止跳过读取 agent 文件的步骤直接编造审查内容。

详见 `AGENTS.md §7.7`。

## 能力边界

- ✅ **仓库级 prompt 注入**：每次 spawn 时读取本目录下的 `.md` 文件，将内容注入子智能体的角色 prompt
- ✅ **可见性**：本目录下的文件可通过文件系统直接查看，团队可随时确认当前生效的自定义角色
- ❌ **运行时原生 agent 注册**：当前宿主不支持将自定义文件注册为系统级可选 agent
- ❌ **独立资源隔离**：自定义 agent 与默认 agent 共享同一 Token Quota 和工具限速
- ❌ **自动更新**：自定义 agent 不会随项目演进而自动改写自身设定

## 生命周期

1. **创建**：通过 `workflow/hr` 技能（`/mywork-hr` 或"招聘agent"触发）交互生成
2. **生效**：文件写入本目录后，下一次审查（并行或降级顺序模式）时自动生效
3. **修改**：直接编辑对应 `.md` 文件，或重新运行 `/mywork-hr` 覆盖（需用户确认）
4. **废弃**：删除对应 `.md` 文件即回退到默认角色；建议在删除前先确认无活跃并行任务引用该角色
5. **消费**：每次审查启动时必须重新读取 agent 文件，不缓存上次读取的内容——保证修改后立即生效

## 静态约束

本目录下的所有 agent 定义文件必须保持**纯静态内容**：

- 禁止包含 `当前时间`、`session_id`、随机数等动态变量
- 禁止包含每次调用会变化的占位符
- 原因：并行技能 spawn 时将此文件内容注入子智能体 prompt 的前缀段；任何动态变量会从注入点开始破坏整个后缀的缓存命中

详见 `AGENTS.md` §7.6。
