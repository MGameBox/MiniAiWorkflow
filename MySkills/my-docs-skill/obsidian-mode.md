# MyDocsSkill Obsidian Mode

## 目标

当项目希望文档更适合 `Obsidian` 使用时，提供双链、MOC 和图谱节点的最小增强规则。

## 何时启用

- 项目明确希望以 `Obsidian` 管理文档。
- 当前任务需要更稳定的文档入口、模块归属关系或项目图谱节点。
- 已经存在 `Docs/` 骨架，但需要增加 `Obsidian` 友好的增强层。

## 核心规则

- 继续使用普通 Markdown 文件，不引入专有格式依赖。
- 双链优先用于稳定入口、模块归属和运行链路。
- 图谱文档只记录高价值结构认知，不记录细碎改动。
- 不为了图谱完整而制造大量低价值节点。

## 支持文档

- `wikilink-writing.md`：双链何时该用、何时不该用
- `moc-and-navigation.md`：`Docs/README.md`、`session-active.md` 与可选 MOC 增强层的导航职责
- `graph-nodes.md`：项目图谱节点的最小职责与新增边界

## 推荐增强范围

在最小 `Docs/` 骨架之外，可按需增加：

- `Docs/Indexes/地图-MOC.md`（可选，仅在需要额外 MOC 导航层时创建）
- `Docs/ProjectGraph/项目总览.md`
- `Docs/Templates/项目图谱模板.md`

## 双链优先使用场景

- 当前阶段到当前重点、下一步入口。
- 架构文档到模块索引、关键计划、关键日志。
- 项目图谱到模块边界、运行链路、当前阶段入口。

## 不建议的做法

- 每句话都加双链。
- 为单次临时 bug 单独建立图谱节点。
- 把代码细节逐项镜像进图谱。

## 启用顺序

1. 先确认本轮任务确实需要文档增强，而不是普通 Markdown 就够用。
2. 若需要导航增强，优先看 `moc-and-navigation.md`。
3. 若需要双链规范，优先看 `wikilink-writing.md`。
4. 若需要图谱节点，优先看 `graph-nodes.md`。
