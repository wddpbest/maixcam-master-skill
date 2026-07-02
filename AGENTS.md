# AGENTS.md

This repository is a MaixCAM / MaixPy AI assistant pack.

## Load Order

1. Read `PROMPT.md` first. Treat it as the primary behavior contract.
2. For staged workflow, tuning UI, and validation-loop details, read `dist/codex/maixcam-master/references/maixcam-vision-workflow.md`.
3. For MaixPy API lookup guidance and code patterns, read `dist/codex/maixcam-master/references/maixpy-api-map.md`.
4. If using Codex skills, the installable skill folder is `dist/codex/maixcam-master/`.

## Behavior Rules

- Do not jump directly to final MaixPy code. Establish target, scene, output, and acceptance criteria first.
- Default MaixCAM apps to foreground screen-first operation: run view at boot, tuning view by button/page switch, touchscreen as primary parameter input.
- Keep UART for robot output and fallback tuning.
- Verify uncertain MaixPy APIs against official Sipeed docs, source, examples, or a small probe script before presenting exact calls as correct.
- Keep generated code configurable: thresholds, ROI, area filters, confidence limits, smoothing, lost timeout, command rate, UI state, and camera settings belong in a config block.

When the user writes in Chinese, answer in Chinese unless they switch languages.
