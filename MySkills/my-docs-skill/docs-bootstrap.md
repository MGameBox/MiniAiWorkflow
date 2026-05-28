# MyDocsSkill Docs Bootstrap

## 目标

当项目缺少最小 `Docs/` 骨架，而当前任务已经需要文档承载时，由 AI 自动补齐最小可用结构。

## 何时启用

- 本轮任务是 M 级或 L 级。
- 本轮需要写计划文档。
- 本轮需要维护当前阶段、当前重点或下一步入口。
- 本轮完成后需要沉淀迭代日志。

如果只是一次性 S 级小修复，且本轮不需要文档承载，可以不初始化。

## 最小创建范围

- `Docs/README.md`
- `Docs/Architecture/项目约定.md`
- `Docs/Current/session-active.md`
- `Docs/Current/project-fingerprint.md`
- `Docs/IterationLogs/README.md`
- `Docs/Templates/计划模板.md`
- `Docs/Templates/迭代日志模板.md`
- `Docs/Archive/README.md`

只有在确实需要时，再补：

- `Docs/Plans/` 下的计划文件
- `Docs/IterationLogs/` 下的具体日志文件
- `Docs/Archive/` 下对应子目录的索引

## 初始化原则

- 先最小闭环，再逐步扩展。
- 允许占位内容，但必须可继续维护。
- 不要求用户手动复制目录或模板。
- 初始化后，后续计划、日志和入口更新仍应服从 `AGENTS.md` 的文档规则。
- 若任务按迭代推进，必须在第一次迭代收口前保证 `Docs/IterationLogs/` 可用。
- 迭代日志不能借用计划文件代写，必须落到 `Docs/IterationLogs/` 下的独立文件。
- 若新增 `Docs/Plans/` 下的计划文件或 `Docs/IterationLogs/` 下的日志文件，文件名必须带 `YYYY-MM-DD-` 年月日前缀。

## 推荐顺序

1. 检查是否已有 `Docs/` 或等价知识库结构。
2. 若无，则创建最小目录与基础文件。
3. 用当前项目信息填入最少必要内容。
4. 当本轮任务结束时，再补计划、日志或入口更新。
