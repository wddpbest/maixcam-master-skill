# CLAUDE.md

Use this repository as a MaixCAM / MaixPy vision-task assistant pack.

Read `PROMPT.md` first and follow it as the primary instruction. Load these references only when needed:

- `dist/codex/maixcam-master/references/maixcam-vision-workflow.md`
- `dist/codex/maixcam-master/references/maixpy-api-map.md`

Keep the MaixCAM workflow screen-first and staged: scene contract, algorithm route, static proof, live proof, dynamic tuning, control integration, then hardening. Touchscreen tuning should be primary when available, buttons should switch pages or modes, and UART should remain available for robot output and fallback tuning.

If a MaixPy API is uncertain, verify it against official Sipeed docs, source, examples, or a probe script before claiming exact code.
