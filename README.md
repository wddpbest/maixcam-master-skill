# maixcam-master-skill

Codex skill for MaixCAM vision recognition, MaixPy workflows, staged validation, and dynamic tuning.

## 快速安装

这个仓库里的实际 skill 目录是：

```text
maixcam-master/
```

安装目标目录是 Codex 的用户 skills 目录：

```text
C:\Users\<你的用户名>\.codex\skills\maixcam-master
```

### 方法 1：用 git 克隆后复制

```powershell
git clone https://github.com/wddpbest/maixcam-master-skill.git
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\maixcam-master-skill\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

### 方法 2：如果已经有本仓库源码

在仓库根目录执行：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

## 验证安装

确认这些文件存在：

```text
C:\Users\<你的用户名>\.codex\skills\maixcam-master\SKILL.md
C:\Users\<你的用户名>\.codex\skills\maixcam-master\agents\openai.yaml
C:\Users\<你的用户名>\.codex\skills\maixcam-master\references\maixcam-vision-workflow.md
C:\Users\<你的用户名>\.codex\skills\maixcam-master\references\maixpy-api-map.md
```

然后重启 Codex，让它重新加载 skills。

## 试用提示词

重启后可以这样测试：

```text
Use $maixcam-master to help me build a MaixCAM color object tracking task. Guide me from camera probe, threshold tuning, live validation, to UART command output for a moving robot.
```

也可以中文测试：

```text
使用 $maixcam-master，帮我做一个 MaixCAM 视觉识别任务：识别红色目标并通过串口输出目标中心偏差。先带我一步一步验证，再设计运动过程中的动态调参。
```
