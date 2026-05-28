---
name: my-super-powers-verification-gate
description: Use when about to say work is complete, fixed, or ready in this project and fresh verification evidence is required first.
---

# MySuperPowers Verification Gate

## 概述

本技能用于把“完成前必须验证”变成一个明确的收口门禁。

## 何时使用

- 准备说“完成了”
- 准备说“修好了”
- 准备结束当前迭代
- 准备更新计划或迭代日志中的结果状态

## 核心规则

1. 没有新鲜验证证据，不得宣称完成。
2. 验证优先使用最能证明结果的命令或检查方式。
3. 如果不能验证，必须明确写出未验证部分与原因。
4. 文档类改动至少要做结构和格式检查。

## 验证优先级

- 代码改动：编译、测试、运行态
- 规则或文档改动：格式检查、链接/入口检查、结构一致性检查
- 引擎改动：以 `EngineSkill` 能提供的验证结果为准
