# MyDocsSkill MOC And Navigation

## 目标

规范 `Docs/` 总导航与当前状态入口的职责，避免把旧 Current 三件套或不存在的 MOC 路径当成默认入口。

## 核心节点

- `Docs/README.md`
- `Docs/Current/session-active.md`
- `Docs/Current/project-fingerprint.md`

## 推荐职责

### Docs/README

- 作为 `Docs/` 总导航页。
- 串起 Current、架构、计划、日志和归档入口。
- 不承载细碎实现细节。

### session-active

- 作为当前阶段、当前重点和下一步入口的单一状态源。
- 保持当前任务、进度、风险和活跃文件可快速读取。
- 避免拆回多个并行状态文件。

### project-fingerprint

- 记录项目身份、根目录和技术栈签名。
- 在审查、分析或执行前提供统一身份校验入口。

## 使用原则

- 先保证导航可用，再追求图谱完整。
- 入口页强调“找得到”，不是“记一切”。
- 结构变化时更新导航，普通小修不强制重写导航。
- 若后续启用额外 Obsidian 增强层，应建立在现有入口稳定之后，不替代 `Docs/README.md` 与 `session-active.md`。
