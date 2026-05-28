# MySkills 本地技能入口

本目录用于存放本项目本地定义的技能挂件。

## 使用原则

- 本地技能用于增强流程，不替代 `AGENTS.md` 的项目级规则。
- 若本地技能与外部默认技能冲突，以本项目规则和 `Docs/` 体系为准。
- 所有文档沉淀仍统一进入项目 `Docs/`，不得自行创建外部默认文档根目录。
- 涉及行业规范、项目命名约定、模块规则或专项技能草稿时，先检查 `workflow/constraints/` 与 `project-constraints/`，再进入 `workflow/hr/` 或 `parallel/`。

## 当前可用技能

- `my-super-powers/`（展示名：`MySuperPowers`）— 流程增强
- `my-docs-skill/`（展示名：`MyDocsSkill`）— 文档系统
- `parallel/`（展示名：`ParallelSkills`）— 子智能体并行编排
- `workflow/claude-setup/` — Claude Code `.claude/` 配置自动释放（初始化时创建 hooks + 路径规则）
- `workflow/hr/` — 子智能体招聘与定制（根据项目需求生成个性化 agent 设定）
- `workflow/constraints/` — 专项约束接线（把行业规范、项目约束、命名约定或技能草稿注册为工作流可加载的约束包）
- `agents/` — 自定义 agent 注册目录（由 `workflow/hr` 产出，由并行技能消费）
- `project-constraints/` — 项目专项约束注册目录（由 `workflow/constraints` 维护，供主智能体后续匹配和装载）

## 推荐阅读顺序

1. `AGENTS.md`
2. `MySkills/README.md`
3. 若任务涉及专项约束接线，先读 `workflow/constraints/SKILL.md` 与 `project-constraints/README.md`
4. 与当前任务匹配的技能总入口（`my-super-powers/SKILL.md`、`my-docs-skill/SKILL.md`、`parallel/README.md` 或 `workflow/hr/SKILL.md`）
5. 与当前任务匹配的分技能文档或支持文档
