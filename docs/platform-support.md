# 平台支持边界

## 主机系统

这个仓库支持在以下主机系统上使用：

| 平台 | 支持内容 |
| --- | --- |
| macOS | 克隆仓库、编辑文档、复制 prompt、安装 Codex skill、准备 MaixCAM 代码 |
| Linux | 克隆仓库、编辑文档、复制 prompt、安装 Codex skill、准备 MaixCAM 代码 |
| Windows | 克隆仓库、编辑文档、复制 prompt、安装 Codex skill、准备 MaixCAM 代码 |

macOS / Linux 使用 `~/.codex/skills/maixcam-master`。Windows 使用 `%USERPROFILE%\.codex\skills\maixcam-master`。

## MaixCAM 硬件

主机系统支持不等于 MaixCAM API 已经在设备上验证。真正的运行行为取决于：

- MaixCAM 型号和固件版本。
- MaixPy 版本。
- 摄像头、屏幕、触摸屏、按键、UART 是否可用。
- 模型文件、分辨率、帧率、内存和显示开销。

如果 API 不确定，应优先查：

- MaixPy source and examples: <https://github.com/sipeed/MaixPy>
- MaixPy docs and API: <https://wiki.sipeed.com/maixpy/>
- MaixCAM hardware docs: <https://wiki.sipeed.com/hardware/zh/maixcam/>

必要时先生成小探针脚本，验证 camera、display、touchscreen、key、UART 的实际调用方式。

## 推荐验证顺序

1. 摄像头和屏幕探针。
2. 触摸屏和按键探针。
3. UART 输出探针。
4. 静态识别。
5. 实时识别叠加。
6. 屏幕运行页和调参页。
7. 运动路径验收。
