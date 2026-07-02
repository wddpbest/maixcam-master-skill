# maixcam-master-skill

MaixCAM / MaixPy 视觉任务 AI 助手包。它把 MaixCAM 识别任务拆成可验证的阶段：摄像头和屏幕探测、算法选择、静态验证、实时叠加显示、触摸屏调参、按键切换页面、UART 输出、运动路径验收。

这个仓库现在同时支持两种用法：

- **普通聊天 AI**：复制 [PROMPT.md](PROMPT.md) 到任意 AI。
- **编程 agent 自动加载**：让 agent 读取仓库根目录的 [AGENTS.md](AGENTS.md)、[CLAUDE.md](CLAUDE.md) 或 [GEMINI.md](GEMINI.md)。
- **Codex skill 安装**：把 [dist/codex/maixcam-master](dist/codex/maixcam-master) 复制到 Codex skills 目录。

## 选择入口

| 使用场景 | 入口 |
| --- | --- |
| 任意聊天 AI，例如网页端 ChatGPT、Claude、Gemini | 复制 `PROMPT.md` |
| 通用编程 agent | 使用 `AGENTS.md` |
| Claude Code | 使用 `CLAUDE.md` |
| Gemini CLI | 使用 `GEMINI.md` |
| Codex skill | 安装 `dist/codex/maixcam-master/` |

## 快速安装 Codex Skill

macOS / Linux:

```bash
git clone https://github.com/wddpbest/maixcam-master-skill.git
cd maixcam-master-skill
mkdir -p "$HOME/.codex/skills"
cp -R dist/codex/maixcam-master "$HOME/.codex/skills/maixcam-master"
```

Windows PowerShell:

```powershell
git clone https://github.com/wddpbest/maixcam-master-skill.git
cd maixcam-master-skill
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\dist\codex\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

安装后重启 Codex，让它重新加载 skills。

## 试用提示词

中文：

```text
使用 maixcam-master，帮我做一个 MaixCAM 视觉识别任务：识别红色目标并通过串口输出目标中心偏差。代码默认前台运行，屏幕显示运行界面，用自带按钮切换运行界面/调参界面，用触摸屏优先调阈值、ROI、面积过滤和输出频率，再带我一步一步验证运动过程中的效果。
```

English:

```text
Use maixcam-master to help me build a MaixCAM color object tracking task. Guide me from camera probing, default run view, touchscreen tuning view, built-in button page switching, live validation, to UART command output for a moving robot.
```

## 文档

- [安装说明](docs/install.md)
- [使用说明](docs/usage.md)
- [AI agent 支持矩阵](docs/ai-agent-support.md)
- [平台支持边界](docs/platform-support.md)

## 重要原则

- 不要一上来写最终代码，先把目标、场景、输出和验收标准说清楚。
- 默认做屏幕优先的前台程序：运行页显示识别叠加，调参页用触摸屏改阈值和 ROI，按键只负责切页或确认。
- UART 首先用于机器人输出，也可以作为备用调参通道。
- MaixPy API 如果不确定，先查 Sipeed 官方文档、源码或跑小探针脚本，不要凭记忆断言。
