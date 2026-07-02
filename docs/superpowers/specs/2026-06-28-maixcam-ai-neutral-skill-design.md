# MaixCAM AI-Neutral Skill Pack Design

## Goal

Refactor this repository from a Codex-centered MaixCAM skill into an AI-neutral MaixCAM vision assistant pack.

The finished repository should support two usage modes:

- Agent auto-loading: coding agents can discover project instructions from root-level files such as `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md`.
- Manual prompt usage: any chat AI can use the package by copying a complete prompt from `PROMPT.md`.

The existing Codex skill does not need to keep its current root path because no device or user installation depends on it yet.

## Non-Goals

- Do not add MaixCAM application code for a specific robot task.
- Do not validate MaixPy APIs against live hardware in this refactor.
- Do not add large generated assets, binaries, or vendored docs.
- Do not make the installable skill folder carry project-level docs such as README or install guides.

## Proposed Structure

```text
maixcam-master-skill/
├── README.md
├── PROMPT.md
├── AGENTS.md
├── CLAUDE.md
├── GEMINI.md
├── docs/
│   ├── ai-agent-support.md
│   ├── install.md
│   ├── platform-support.md
│   └── usage.md
├── dist/
│   └── codex/
│       └── maixcam-master/
│           ├── SKILL.md
│           ├── agents/
│           │   └── openai.yaml
│           └── references/
│               ├── maixcam-vision-workflow.md
│               └── maixpy-api-map.md
└── docs/superpowers/specs/
    └── 2026-06-28-maixcam-ai-neutral-skill-design.md
```

The `dist/codex/maixcam-master/` directory is the installable Codex skill. The repository root is the universal package entry point.

## Root Entry Files

`PROMPT.md` is the complete universal prompt. It should be self-contained enough to paste into a normal AI chat while still pointing to the reference files when the AI has file access.

`AGENTS.md` is the general coding-agent instruction file. It should tell agents to read `PROMPT.md` first, then use the detailed references only when needed.

`CLAUDE.md` and `GEMINI.md` are thin adapters for those agent ecosystems. They should avoid duplicating the full skill body and instead point to the universal prompt and references.

## Codex Skill Package

Keep the Codex skill concise and compliant:

- `SKILL.md` keeps YAML frontmatter with only `name` and `description`.
- The body contains core MaixCAM workflow instructions and reference routing.
- Detailed workflow and API lookup material remain in `references/`.
- `agents/openai.yaml` remains UI metadata for Codex.
- No README, install guide, changelog, or project docs live inside the skill folder.

## Documentation

`README.md` should become the practical landing page:

- Explain what the package is.
- Show which file to use for each AI.
- Point to install and usage docs.
- Keep quick-start examples in Chinese and English.

`docs/install.md` should include macOS, Linux, and Windows commands. It should cover installing the Codex skill from `dist/codex/maixcam-master/` to:

- macOS/Linux: `~/.codex/skills/maixcam-master`
- Windows PowerShell: `$env:USERPROFILE\.codex\skills\maixcam-master`

`docs/usage.md` should explain:

- Manual chat AI usage with `PROMPT.md`.
- Agent usage with root instruction files.
- Codex skill installation and invocation.

`docs/platform-support.md` should separate host platform support from MaixCAM hardware support:

- macOS and Linux are supported for editing, prompt usage, repo management, and skill installation.
- Windows remains supported.
- Actual MaixCAM API behavior must still be verified on target firmware and hardware.

`docs/ai-agent-support.md` should map supported AI surfaces to entry files:

- Generic agents: `AGENTS.md`
- Claude Code: `CLAUDE.md`
- Gemini CLI: `GEMINI.md`
- Codex: `dist/codex/maixcam-master/`
- Any chat AI: `PROMPT.md`

## Content Rules

- Keep MaixCAM domain behavior consistent with the existing skill: staged validation, screen-first UI, touch-first tuning, built-in button page switching, UART for robot output and fallback tuning, and hardware API verification before claiming exact calls.
- Prefer Chinese-first docs with English-readable headings or short English examples where useful.
- Avoid duplicating long workflow content across multiple adapter files.
- Keep the installable Codex skill lean; put user-facing repository docs outside it.
- Remove `.DS_Store` from the working tree and add an ignore rule for it.

## Validation

After implementation:

- Run `git status --short --branch` to confirm intended file moves and additions.
- Inspect root entry files for broken paths.
- Validate the Codex skill shape manually: `dist/codex/maixcam-master/SKILL.md`, `agents/openai.yaml`, and `references/` must exist.
- If the local skill validation scripts are available, run `quick_validate.py` against `dist/codex/maixcam-master`.
- Confirm no unfinished markers remain in shipped docs.

## Open Decisions Resolved

- Backward compatibility with the old root `maixcam-master/` folder is not required.
- The package should support both agent auto-loading and manual prompt copying.
- macOS, Linux, and Windows install instructions are all required.
