# 安装说明

这个仓库有两类安装或使用方式：

- 普通聊天 AI：不需要安装，复制 `PROMPT.md` 即可。
- Codex skill：把 `dist/codex/maixcam-master/` 复制到 Codex skills 目录。

## macOS / Linux 安装 Codex Skill

```bash
git clone https://github.com/wddpbest/maixcam-master-skill.git
cd maixcam-master-skill
mkdir -p "$HOME/.codex/skills"
cp -R dist/codex/maixcam-master "$HOME/.codex/skills/maixcam-master"
```

如果已经在仓库根目录：

```bash
mkdir -p "$HOME/.codex/skills"
rm -rf "$HOME/.codex/skills/maixcam-master"
cp -R dist/codex/maixcam-master "$HOME/.codex/skills/maixcam-master"
```

安装后重启 Codex。

## Windows PowerShell 安装 Codex Skill

```powershell
git clone https://github.com/wddpbest/maixcam-master-skill.git
cd maixcam-master-skill
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\dist\codex\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

如果已经在仓库根目录：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Remove-Item -Recurse -Force "$env:USERPROFILE\.codex\skills\maixcam-master" -ErrorAction SilentlyContinue
Copy-Item -Recurse -Force ".\dist\codex\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

安装后重启 Codex。

## 验证 Codex Skill 文件

确认这些文件存在：

```text
~/.codex/skills/maixcam-master/SKILL.md
~/.codex/skills/maixcam-master/agents/openai.yaml
~/.codex/skills/maixcam-master/references/maixcam-vision-workflow.md
~/.codex/skills/maixcam-master/references/maixpy-api-map.md
```

Windows 对应路径是：

```text
%USERPROFILE%\.codex\skills\maixcam-master\SKILL.md
%USERPROFILE%\.codex\skills\maixcam-master\agents\openai.yaml
%USERPROFILE%\.codex\skills\maixcam-master\references\maixcam-vision-workflow.md
%USERPROFILE%\.codex\skills\maixcam-master\references\maixpy-api-map.md
```

## 普通 AI 使用

打开 `PROMPT.md`，把全文复制到任意 AI 的系统提示词、项目说明或当前对话开头，然后提出具体 MaixCAM 任务。
