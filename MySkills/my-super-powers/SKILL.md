---
name: my-super-powers
description: Use when working inside this project and a stronger workflow layer is needed for planning, debugging, verification, or task decomposition without leaving the project's Docs system.
---

# MySuperPowers

## 概述

`MySuperPowers` 是本项目的本地流程增强挂件。

它吸收外部 `SuperPowers` 中可复用的方法，但不继承其默认文档目录、默认优先级和默认执行主权。项目主规则始终以 `AGENTS.md` 与 `Docs/` 体系为准。

## 何时使用

- 任务已经进入方案、计划、调试、验证或评审阶段，需要比主规则更细的执行步骤。
- 需要借用外部 `SuperPowers` 的流程思想，但不能让输出落到 `docs/superpowers/` 或其他外部默认目录。
- 需要把大任务拆成更清晰的迭代、把 bug 排查步骤收紧、或把“完成前验证”写成明确动作。

## 不要用于

- 覆盖 `AGENTS.md` 的权威规则。
- 替代 `EngineSkill` 处理引擎编辑器相关任务。
- 创建项目之外的新文档体系。

## 权威边界

1. `AGENTS.md` 高于本技能。
2. 所有文档沉淀只能进入项目 `Docs/` 体系。
3. 禁止创建或使用 `docs/superpowers/`、`specs/`、`plans/` 等外部默认文档根目录，除非用户明确要求。
4. `MySuperPowers` 只提供方法，不接管任务分级、迭代确认和完成定义。

## 默认工作方式

- 先按 `AGENTS.md` 判断当前任务是 S、M 还是 L。
- 默认以“一个迭代一个闭环”为推进单位，而不是连续推进多个迭代。
- S 级：优先借用“完成前验证”与“问题根因排查”思路，不扩大流程。
- M 级：先给最小方案，确认后执行；需要时借用计划拆解与验证清单。
- L 级：借用头脑风暴、方案拆分、计划分步、阶段验证，但所有计划和日志仍写入 `Docs/`。

## 能力映射

- 方案生成：借鉴 `brainstorming`，但方案与计划文档写入项目 `Docs/Plans/` 或用户指定位置。
- 计划拆解：借鉴 `writing-plans`，但只服务于项目本地计划模板与分批执行要求。
- 调试排查：借鉴 `systematic-debugging`，要求先找根因，再改代码。
- 测试优先：借鉴 `test-driven-development`，在适用时先验证失败场景，再实现。
- 完成收口：借鉴 `verification-before-completion`，任何完成声明前都要有新鲜验证证据。

## 配套文档

详细映射与落地规则见 `workflow-map.md`。

迭代与步骤的执行边界见 `iteration-governor.md`。

执行模式选择见 `execution-modes.md`。

主动请求评审与接收评审反馈的分工见 `request-review.md` 与 `receive-review.md`。

方案或计划形成后的自检回路见 `plan-review.md`。

## 分技能入口

- `brainstorming-flow/SKILL.md`
- `planning-flow/SKILL.md`
- `debugging-flow/SKILL.md`
- `verification-gate/SKILL.md`
- `review-loop/SKILL.md`