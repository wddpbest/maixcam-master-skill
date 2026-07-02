# AI Agent 支持矩阵

| AI / 工具 | 推荐入口 | 说明 |
| --- | --- | --- |
| 任意聊天 AI | `PROMPT.md` | 复制全文到对话或系统提示词 |
| 通用编程 agent | `AGENTS.md` | 仓库根目录自动加载或手动读取 |
| Claude Code | `CLAUDE.md` | 轻量适配文件，主规则仍在 `PROMPT.md` |
| Gemini CLI | `GEMINI.md` | 轻量适配文件，主规则仍在 `PROMPT.md` |
| Codex | `dist/codex/maixcam-master/` | 可复制到 Codex skills 目录安装 |
| Cursor / Windsurf 等 IDE agent | `AGENTS.md` 或 `PROMPT.md` | 如果支持项目指令，优先用 `AGENTS.md`；否则复制 `PROMPT.md` |

## 设计原则

- `PROMPT.md` 是唯一完整的通用提示词。
- 根目录 agent 文件只做路由和约束，不复制长篇工作流。
- Codex skill 保持可安装结构：`SKILL.md`、`agents/openai.yaml`、`references/`。
- references 保留详细 MaixCAM 工作流和 MaixPy API 查证路径。

## 给新 AI 使用的最小步骤

1. 先尝试让它读取 `PROMPT.md`。
2. 如果它能读取仓库文件，让它按需读取 `dist/codex/maixcam-master/references/`。
3. 如果它不能读取文件，就复制 `PROMPT.md` 全文。
4. 提出具体 MaixCAM 任务，并要求它先给分阶段验证计划，再写代码。
