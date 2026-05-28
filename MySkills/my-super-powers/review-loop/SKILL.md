---
name: my-super-powers-review-loop
description: Use when this project needs code review handling or structured feedback processing without turning every small task into a heavy mandatory gate.
---

# MySuperPowers Review Loop

## 概述

本技能吸收外部 `requesting-code-review` 与 `receiving-code-review` 的优点，但不把每个小任务都强制升级成重评审流程。

## 何时使用

- M 级任务进入关键迭代
- L 级任务完成某个阶段
- 收到外部评审意见，需要先核实再落地

## 核心规则

1. 评审是增强环节，不替代项目主流程。
2. 收到反馈先核实，再修改，不表演式认同。
3. 对外部建议要检查是否符合本项目真实上下文。
4. 未修复的重要问题不进入下一关键迭代。
5. 主动请求评审时，看 `request-review.md`；接收评审反馈时，看 `receive-review.md`。

## 推荐动作

- 对重要迭代请求一次结构化评审。
- 对收到的反馈逐项核实，必要时向用户确认取舍。
- 若评审结论影响范围或计划，应同步更新 `Docs/Plans/` 或 `Docs/Current/`。
