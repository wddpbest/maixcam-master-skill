# 使用说明

## 普通聊天 AI

复制 `PROMPT.md` 全文到任意 AI，然后继续描述任务。例如：

```text
我现在要用 MaixCAM 识别红色球，串口输出中心偏差。请先带我验证摄像头和屏幕，再写可触摸调参的 MaixPy 程序。
```

如果 AI 可以读取文件，把仓库也提供给它，让它按需读取：

- `dist/codex/maixcam-master/references/maixcam-vision-workflow.md`
- `dist/codex/maixcam-master/references/maixpy-api-map.md`

## 编程 Agent

把这个仓库作为项目根目录打开即可。常见入口：

- 通用 agent：`AGENTS.md`
- Claude Code：`CLAUDE.md`
- Gemini CLI：`GEMINI.md`

这些文件会把 agent 路由到 `PROMPT.md` 和 references，不需要重复复制长提示词。

## Codex Skill

安装 `dist/codex/maixcam-master/` 后，可以这样调用：

```text
Use $maixcam-master to help me build a MaixCAM color object tracking task.
```

中文也可以：

```text
使用 $maixcam-master，帮我做一个 MaixCAM 视觉识别任务：识别红色目标并通过串口输出目标中心偏差。
```

## 推荐工作流

1. 先描述目标、场景、输出接口和验收标准。
2. 让 AI 选择最简单可行的算法路线。
3. 先跑摄像头和屏幕探针脚本。
4. 做静态识别验证，再做实时叠加显示。
5. 加入运行页和调参页。
6. 用触摸屏调阈值、ROI、面积过滤、输出频率。
7. 通过 UART 输出机器人命令。
8. 在真实运动路径上验收，而不是只在桌面上看一次识别结果。
