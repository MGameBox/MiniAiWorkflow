---
name: my-docs-skill
description: Use when this project needs Docs initialization, Docs structure maintenance, or Obsidian-friendly documentation without leaving the project's Docs system.
---

# MyDocsSkill

## 概述

`MyDocsSkill` 是本项目的本地文档技能包。

它吸收 `Docs` 自动初始化说明与 `Obsidian` 增强补充中的可复用规则，但不替代 `AGENTS.md` 的主流程判断。

## 何时使用

- 项目缺少最小 `Docs/` 骨架，需要自动初始化。
- 当前任务需要计划、日志、当前阶段或下一步入口等文档承载。
- 项目希望文档写法更适合 `Obsidian`，需要双链、MOC 或图谱节点支持。

## 不要用于

- 替代 `AGENTS.md` 的任务分级与执行边界。
- 为了完整而创建大量低价值空文档。
- 在纯 S 级小任务中机械初始化整套文档体系。

## 权威边界

1. 顶层流程仍以 `AGENTS.md` 为准。
2. 文档根目录仍然是项目 `Docs/`。
3. `MyDocsSkill` 只负责文档初始化、结构增强和文档写法约束。
4. 如果任务同时使用 `MySuperPowers`，则 `MySuperPowers` 负责流程，`MyDocsSkill` 负责文档层。

## 组成文件

- `README.md`
- `docs-bootstrap.md`
- `obsidian-mode.md`
- `iteration-log-policy.md`
- `wikilink-writing.md`
- `moc-and-navigation.md`
- `graph-nodes.md`

## 推荐顺序

1. 先看 `AGENTS.md`
2. 再看 `MySkills/README.md`
3. 再看 `my-docs-skill/SKILL.md`
4. 需要初始化 `Docs/` 时进入 `docs-bootstrap.md`
5. 需要迭代日志规则时进入 `iteration-log-policy.md`
6. 需要 `Obsidian` 增强时先看 `obsidian-mode.md`，再按需要进入对应支持文档

## 补充约束

- 迭代日志必须写成 `Docs/IterationLogs/` 下的独立文件，不能只在计划文档中顺手补一句结果状态。
- `Docs/Plans/` 下的计划文件与 `Docs/IterationLogs/` 下的日志文件，文件名都必须带 `YYYY-MM-DD-` 年月日前缀。
