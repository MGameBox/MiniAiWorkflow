---
name: claude-setup
description: "在 AI 协作启动工作流初始化阶段，自动创建 .claude/ 配置（路径规则 + hooks + settings.json）。用户在初始化对话中确认启用后，AI 读取本技能并按模板生成所有文件。"
tools: Read, Write, Bash
model: sonnet
---

# Claude Code 配置自动释放

## 触发时机

- `AI协作启动工作流` 初始化阶段
- 用户确认需要启用 Claude Code 增强（hooks + 路径规则）
- 项目根目录不存在 `.claude/` 配置

> `.claude/` 由初始化流程自动创建；用户不需要手动复制当前项目的 `.claude/` 目录，也不需要预创建空目录。

## 自动创建的文件

启用后，AI 会在项目根目录创建以下文件：

```
.claude/
├── rules/
│   ├── docs-plans.md      ← Plans 文档命名规范
│   └── docs-logs.md       ← 迭代日志命名规范
├── hooks/
│   ├── hook_runner.py     ← 默认 hook 入口（跨平台）
│   ├── session-start.sh   ← 辅助 shell 版本：显示项目状态
│   ├── detect-gaps.sh     ← 辅助 shell 版本：检测项目缺口
│   ├── pre-compact.sh     ← 辅助 shell 版本：压缩前记录时间戳
│   └── post-compact.sh    ← 辅助 shell 版本：压缩后提示恢复状态
└── settings.json          ← Hook 注册 + 权限规则
```

## 文件模板

### settings.json

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "Bash(git status*)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Bash(git branch*)",
      "Bash(ls *)",
      "Bash(dir *)",
      "Bash(cat *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(git push -f *)",
      "Bash(git reset --hard*)",
      "Bash(git clean -f*)",
      "Bash(*>.env*)",
      "Bash(cat *.env*)",
      "Read(**/.env*)"
    ]
  },
  "hooks": {
    "SessionStart": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "python .claude/hooks/hook_runner.py session-start",
            "timeout": 10
          },
          {
            "type": "command",
            "command": "python .claude/hooks/hook_runner.py detect-gaps",
            "timeout": 10
          }
        ]
      }
    ],
    "PreCompact": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "python .claude/hooks/hook_runner.py pre-compact",
            "timeout": 10
          }
        ]
      }
    ],
    "PostCompact": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "python .claude/hooks/hook_runner.py post-compact",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### rules/docs-plans.md

```markdown
# Rule: Plan Documents

Applies to: `Docs/Plans/**`

## Constraints

- 文件名必须带 `YYYY-MM-DD-` 年月日前缀
- 文件必须包含：目标、范围（含非目标）、整体迭代总览（表格）、推荐推进方向
- 计划完成后必须停止等待用户确认，禁止自动开始第一次迭代
- 跨模块计划推荐先经过 `architecture-review` 角度预审；可复用 `team-review` 中的 `architecture-review` 角色，但不要求在计划阶段运行完整 `team-review`
```

### review gate 约定

- `Docs/Current/session-active.md` 中使用 `REVIEW-CONFIG` 作为 machine-readable 审查配置源
- 默认消费脚本：`.claude/hooks/review-gate.sh`
- Windows / 无 bash 的本地降级路径：`.claude/hooks/review-gate.cmd`

### rules/docs-logs.md

```markdown
# Rule: Iteration Log Documents

Applies to: `Docs/IterationLogs/**`

## Constraints

- 文件名必须带 `YYYY-MM-DD-` 年月日前缀
- 必须为每个已完成迭代创建**独立日志文件**
- 禁止只在计划文件或其他文档中补一句状态来替代独立日志
- 最小内容：日期、迭代目标、关键步骤、影响范围、验证结果、下一步状态
```

### hooks/hook_runner.py

默认 hook 入口改为 `.claude/hooks/hook_runner.py`。

最小要求：
- 支持 `session-start`、`detect-gaps`、`pre-compact`、`post-compact` 四种模式
- `session-start` 读取 `Docs/Current/session-active.md` 中的状态摘要与 `REVIEW-CONFIG`
- `detect-gaps` 检查 Plans、Current、Architecture 与源码目录缺口
- `pre-compact` 负责追加 `COMPACT-NOTE`，并避免制造多余空行
- `post-compact` 输出恢复上下文提示

实现时优先参考当前仓库中的同名文件，保持与现行 `.claude/settings.json` 一致。

### hooks/session-start.sh

```bash
#!/bin/bash
# session-start.sh — Session 打开时显示当前状态摘要

set -e

SESSION_FILE="Docs/Current/session-active.md"

if [ -f "$SESSION_FILE" ]; then
    echo ""
    echo "=== 当前项目状态 ==="
    echo ""
    if grep -q "<!-- STATUS -->" "$SESSION_FILE" 2>/dev/null; then
        grep -A2 "<!-- STATUS -->" "$SESSION_FILE" | grep -v "STATUS" | head -3
        echo ""
    fi
    if grep -q "## 当前任务" "$SESSION_FILE"; then
        sed -n '/## 当前任务/,/## /p' "$SESSION_FILE" | head -5 | tail -n +3
        echo ""
    fi
    echo "--- 待办进度 ---"
    grep "\[ \]" "$SESSION_FILE" | head -5 || echo "(暂无未完成任务)"
    echo ""
    echo "完整状态见: $SESSION_FILE"
    echo ""
else
    echo "[session-start] 未找到 $SESSION_FILE，建议初始化 Docs/Current/session-active.md"
fi
```

### hooks/detect-gaps.sh

```bash
#!/bin/bash
# detect-gaps.sh — 检测项目通用缺口

set -e

PLANS_DIR="Docs/Plans"
SESSION_FILE="Docs/Current/session-active.md"

# 检测1: 有 Plans 文档但源码极少
SOURCE_COUNT=$(find . -maxdepth 3 -type f \( -name "*.cs" -o -name "*.js" -o -name "*.ts" -o -name "*.gd" -o -name "*.cpp" -o -name "*.py" \) | wc -l)
if [ "$SOURCE_COUNT" -lt 5 ]; then
    if [ -d "$PLANS_DIR" ] && [ "$(ls -A "$PLANS_DIR" 2>/dev/null)" ]; then
        echo ""
        echo "[detect-gaps] 检测到 Plans/ 下已有计划文档，但源码文件极少（$SOURCE_COUNT 个）。"
        echo "建议检查是否需要启动项目开发。"
        echo ""
    fi
fi

# 检测2: 无 session-active.md
if [ ! -f "$SESSION_FILE" ] && [ -d "Docs/Current" ]; then
    echo ""
    echo "[detect-gaps] 未找到 Docs/Current/session-active.md，但 Current/ 目录存在。"
    echo "建议创建统一状态文件以替代分散的进度记录。"
    echo ""
fi

# 检测3: 代码存在但无 Architecture 文档
if [ "$SOURCE_COUNT" -ge 5 ] && [ ! -f "Docs/Architecture/项目约定.md" ] && [ ! -f "Docs/Architecture/README.md" ]; then
    echo ""
    echo "[detect-gaps] 检测到源码目录存在，但 Docs/Architecture/ 下缺少架构文档。"
    echo "建议创建项目约定或架构说明文档。"
    echo ""
fi
```

### hooks/pre-compact.sh

```bash
#!/bin/bash
# pre-compact.sh — 压缩前保存 session 进度

set -e

SESSION_FILE="Docs/Current/session-active.md"

if [ -f "$SESSION_FILE" ]; then
    echo "" >> "$SESSION_FILE"
    echo "<!-- COMPACT-NOTE -->" >> "$SESSION_FILE"
    echo "Session 于 $(date '+%Y-%m-%d %H:%M:%S') 触发 compaction。" >> "$SESSION_FILE"
    echo "恢复时请重新读取本文件获取完整上下文。" >> "$SESSION_FILE"
    echo "<!-- /COMPACT-NOTE -->" >> "$SESSION_FILE"
fi
```

### hooks/post-compact.sh

```bash
#!/bin/bash
# post-compact.sh — 压缩后提示恢复上下文

set -e

SESSION_FILE="Docs/Current/session-active.md"

if [ -f "$SESSION_FILE" ]; then
    echo ""
    echo "[post-compact] Session 已压缩。请重新读取 Docs/Current/session-active.md 恢复上下文。"
    echo ""
fi
```

## 创建步骤

AI 按以下顺序自动创建：

1. `mkdir -p .claude/rules .claude/hooks`
2. 写入 `settings.json`
3. 写入 `rules/docs-plans.md`
4. 写入 `rules/docs-logs.md`
5. 写入 `hook_runner.py` 与辅助 hook 脚本
6. 报告用户创建结果

## 注意事项

- 如果项目已有 `.claude/settings.json`，本技能不会覆盖，只提示用户手动合并
- `.claude/settings.local.json` 是本地配置，不在本技能范围内
- 默认 hook 通过 Python runner 执行；直接运行 `.sh` 辅助脚本时仍需要 bash 环境
