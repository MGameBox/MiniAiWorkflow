# MySuperPowers 技能包说明

`MySuperPowers` 是本项目的本地流程增强技能包。

它的目标不是复制外部 `SuperPowers` 的整套默认行为，而是把其中有价值的工作流，改造成服从本项目 `AGENTS.md` 与 `Docs/` 体系的本地版本。

## 文件结构

- 目录路径：`my-super-powers/`
- `SKILL.md`：总入口与总规则
- `workflow-map.md`：外部流程到本地规则的映射
- `iteration-governor.md`：迭代与步骤的推进边界
- `execution-modes.md`：单会话执行与子代理执行的选择规则
- `plan-review.md`：方案与计划的自检回路
- `request-review.md`：何时主动请求评审
- `receive-review.md`：如何接收并处理评审反馈
- `brainstorming-flow/SKILL.md`：头脑风暴与设计澄清流程
- `planning-flow/SKILL.md`：方案与计划流程
- `debugging-flow/SKILL.md`：系统化调试流程
- `verification-gate/SKILL.md`：完成前验证流程
- `review-loop/SKILL.md`：评审与反馈处理流程

## 使用顺序

1. 先看 `AGENTS.md`
2. 再看 `MySkills/README.md`
3. 再看 `my-super-powers/SKILL.md`
4. 然后按任务类型进入具体分技能

## 核心限制

- 不得写入 `docs/superpowers/`、`specs/`、`plans/` 等外部默认文档根目录。
- 不得绕过项目的 S/M/L 分级。
- 不得绕过“每个迭代完成后等待用户确认”的执行边界。
- 不得在未完成当前迭代验证、日志与用户确认前推进下一迭代。
- 不得替代 `EngineSkill` 处理引擎专属任务。
