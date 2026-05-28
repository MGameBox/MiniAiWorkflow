---
name: my-super-powers-debugging-flow
description: Use when a bug, test failure, compile failure, or unexpected behavior in this project needs root-cause analysis before any fix is attempted.
---

# MySuperPowers Debugging Flow

## 概述

本技能吸收外部 `systematic-debugging` 的方法，但把输出和收口方式改成项目本地规则。

## 何时使用

- 测试失败
- 编译失败
- 运行行为异常
- 已经尝试过修复但仍不确定根因

## 核心规则

1. 先读错误信息，先稳定复现，再追根因。
2. 没有根因调查，不进入修复。
3. 不允许一次性叠加多个猜测性修复。
4. 若调查结果需要沉淀，只记录高价值结论到 `Docs/IterationLogs/` 或相关文档。

## 推荐顺序

1. 记录错误与复现步骤。
2. 查最近改动和相关上下文。
3. 找到最接近的工作正常样例。
4. 比较差异，形成单一假设。
5. 用最小改动验证假设。
6. 只有在假设被验证后才正式修复。
