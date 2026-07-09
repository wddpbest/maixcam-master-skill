# maixcam-master-skill

Codex skill for MaixCAM vision recognition, MaixPy workflows, staged validation, screen-first touch/button tuning, and dynamic robot output.

## 目录结构

这个仓库里的实际 skill 目录是：

```text
maixcam-master/
```

里面的核心文件是：

```text
maixcam-master/SKILL.md
maixcam-master/agents/openai.yaml
maixcam-master/references/maixcam-vision-workflow.md
maixcam-master/references/maixpy-api-map.md
```

## Codex 使用方法

### Windows 安装

```text
C:\Users\<你的用户名>\.codex\skills\maixcam-master
```

用 git 克隆后复制：

```powershell
git clone https://github.com/wddpbest/maixcam-master-skill.git
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\maixcam-master-skill\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

如果已经有本仓库源码，在仓库根目录执行：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

### macOS / Linux 安装

```bash
git clone https://github.com/wddpbest/maixcam-master-skill.git
mkdir -p ~/.codex/skills
cp -R maixcam-master-skill/maixcam-master ~/.codex/skills/maixcam-master
```

如果已经在仓库根目录：

```bash
mkdir -p ~/.codex/skills
cp -R maixcam-master ~/.codex/skills/maixcam-master
```

重启 Codex，让它重新加载 skills。

## Claude Code 使用方法

Claude Code 的自定义 skills 也是文件夹式结构。把 `maixcam-master/` 复制到用户级 skills 目录：

```bash
git clone https://github.com/wddpbest/maixcam-master-skill.git
mkdir -p ~/.claude/skills
cp -R maixcam-master-skill/maixcam-master ~/.claude/skills/maixcam-master
```

也可以只在某个项目中启用：

```bash
mkdir -p .claude/skills
cp -R maixcam-master .claude/skills/maixcam-master
```

重启 Claude Code 后，可以直接说：

```text
Use maixcam-master to help me build a MaixCAM color blob tracking app with screen tuning and H.264 capture.
```

或者中文：

```text
使用 maixcam-master，帮我做 MaixCAM 色块识别，要求有运行界面、触摸屏调参、PHOTO/REC 数据采集和 H.264 录制。
```

## Claude.ai / Web 版 Claude 使用方法

Claude.ai 的 Skills 通常以压缩包导入。打包时要让压缩包根目录直接包含 `maixcam-master/` 文件夹：

```bash
git clone https://github.com/wddpbest/maixcam-master-skill.git
cd maixcam-master-skill
zip -r ../maixcam-master.zip maixcam-master
```

如果你已经在仓库根目录，可以这样打包：

```bash
zip -r maixcam-master.zip maixcam-master
```

然后在 Claude 的 Skills 设置里上传这个 zip。上传后用类似提示词触发：

```text
Use maixcam-master. I want to build a MaixCAM vision task and need staged validation, screen-first tuning, and real-device debugging guidance.
```

## 其他 AI 的用法

如果某个 AI 工具不支持 Skills 文件夹，可以把这几个文件作为上下文上传或粘贴：

```text
maixcam-master/SKILL.md
maixcam-master/references/maixcam-vision-workflow.md
maixcam-master/references/maixpy-api-map.md
```

推荐在对话开头加一句：

```text
请先阅读 SKILL.md，并在需要 MaixCAM/MaixPy API、屏幕调参、H.264 录制或真机验证细节时查阅 references 目录。不要凭空假设 API；如果把握低于 95%，先提问或给出探针脚本。
```

对 Cursor、VS Code Agent、通用 ChatGPT/Gemini/Kimi/DeepSeek 这类工具，可以直接把 `maixcam-master/` 目录拖进项目，或把上述文件作为项目知识库/上下文文件使用。

## 验证安装

Codex 安装后确认这些文件存在：

```powershell
Test-Path "$env:USERPROFILE\.codex\skills\maixcam-master\SKILL.md"
Test-Path "$env:USERPROFILE\.codex\skills\maixcam-master\references\maixpy-api-map.md"
```

Claude Code 安装后确认这些文件存在：

```text
~/.claude/skills/maixcam-master/SKILL.md
~/.claude/skills/maixcam-master/references/maixpy-api-map.md
```

## 试用提示词

Codex 可以这样测试：

```text
Use $maixcam-master to help me build a MaixCAM color object tracking task. Guide me from camera probe, default run view, touchscreen tuning view, built-in button page switching, live validation, to UART command output for a moving robot.
```

也可以中文测试：

```text
使用 $maixcam-master，帮我做一个 MaixCAM 视觉识别任务：识别红色目标并通过串口输出目标中心偏差。代码默认前台运行，屏幕显示运行界面，用自带按钮切换运行界面/调参界面，用触摸屏优先调阈值、ROI、面积过滤和输出频率，再带我一步一步验证运动过程中的效果。
```
