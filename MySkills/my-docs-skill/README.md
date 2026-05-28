# MyDocsSkill 技能包说明

`MyDocsSkill` 是本项目的本地文档技能包，负责两件事：

- `Docs/` 最小骨架自动初始化
- `Obsidian` 友好的文档增强写法与图谱节点约束

## 文件结构

- 目录路径：`my-docs-skill/`
- `SKILL.md`：总入口与总边界
- `docs-bootstrap.md`：`Docs/` 自动初始化规则
- `iteration-log-policy.md`：迭代日志触发条件与写法约束
- `obsidian-mode.md`：`Obsidian` 增强层总览
- `wikilink-writing.md`：双链写法与使用边界
- `moc-and-navigation.md`：MOC 与导航页职责
- `graph-nodes.md`：图谱节点职责与新增边界

## 使用顺序

1. 先看 `AGENTS.md`
2. 再看 `MySkills/README.md`
3. 再看 `my-docs-skill/SKILL.md`
4. 按任务需要进入具体文档

## 推荐分流

- 缺少 `Docs/` 骨架：看 `docs-bootstrap.md`
- 想明确什么时候必须写迭代日志：看 `iteration-log-policy.md`
- 想启用 `Obsidian` 增强：先看 `obsidian-mode.md`
- 想规范双链：看 `wikilink-writing.md`
- 想梳理导航与入口：看 `moc-and-navigation.md`
- 想控制图谱节点范围：看 `graph-nodes.md`

## 核心限制

- 所有文档仍写入项目 `Docs/`。
- 不创建项目外部的默认文档根目录。
- 不为图谱完整性制造低价值节点。
- 不在不需要文档承载的 S 级任务中强行扩张文档系统。
- 不得用计划文件中的一句状态替代 `Docs/IterationLogs/` 下的独立迭代日志文件。
