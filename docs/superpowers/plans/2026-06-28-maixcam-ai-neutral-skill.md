# MaixCAM AI-Neutral Skill Pack Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Convert the repository into a MaixCAM assistant pack that works both as auto-loaded agent instructions and as a copy-paste prompt for any AI.

**Architecture:** The repository root becomes the universal package entry point. The installable Codex skill moves under `dist/codex/maixcam-master/`, while root adapter files route agents to `PROMPT.md` and detailed references.

**Tech Stack:** Markdown documentation, Codex skill folder conventions, shell validation commands.

---

## File Structure

- Modify: `README.md` as the landing page for all AI usage modes.
- Create: `PROMPT.md` as the complete universal prompt for manual chat AI use.
- Create: `AGENTS.md` as the generic coding-agent entry point.
- Create: `CLAUDE.md` as the Claude Code adapter.
- Create: `GEMINI.md` as the Gemini CLI adapter.
- Create: `docs/install.md` for macOS, Linux, and Windows install commands.
- Create: `docs/usage.md` for manual and agent usage.
- Create: `docs/platform-support.md` for host and hardware support boundaries.
- Create: `docs/ai-agent-support.md` for AI surface mapping.
- Move: `maixcam-master/` to `dist/codex/maixcam-master/`.
- Create: `.gitignore` with `.DS_Store`.
- Remove: tracked or untracked `.DS_Store` artifacts from the worktree.

### Task 1: Move Codex Skill Package

**Files:**
- Move: `maixcam-master/SKILL.md` to `dist/codex/maixcam-master/SKILL.md`
- Move: `maixcam-master/agents/openai.yaml` to `dist/codex/maixcam-master/agents/openai.yaml`
- Move: `maixcam-master/references/maixcam-vision-workflow.md` to `dist/codex/maixcam-master/references/maixcam-vision-workflow.md`
- Move: `maixcam-master/references/maixpy-api-map.md` to `dist/codex/maixcam-master/references/maixpy-api-map.md`

- [ ] **Step 1: Create the target folder**

Run:

```bash
mkdir -p dist/codex
```

Expected: command exits with status 0.

- [ ] **Step 2: Move the existing skill folder**

Run:

```bash
mv maixcam-master dist/codex/maixcam-master
```

Expected: `dist/codex/maixcam-master/SKILL.md` exists and the old root `maixcam-master/` folder no longer exists.

- [ ] **Step 3: Verify the installable skill shape**

Run:

```bash
test -f dist/codex/maixcam-master/SKILL.md
test -f dist/codex/maixcam-master/agents/openai.yaml
test -f dist/codex/maixcam-master/references/maixcam-vision-workflow.md
test -f dist/codex/maixcam-master/references/maixpy-api-map.md
```

Expected: each command exits with status 0.

### Task 2: Add Universal Prompt

**Files:**
- Create: `PROMPT.md`

- [ ] **Step 1: Write `PROMPT.md`**

Create a complete prompt that includes:

- Role: MaixCAM vision task lead.
- Required workflow: scene contract, algorithm route, static proof, live proof, dynamic tuning, control integration, hardening.
- Runtime defaults: foreground screen-first run view, button page switching, touchscreen tuning view, UART robot output and fallback tuning.
- API discipline: verify uncertain MaixPy APIs against official docs or source.
- Output expectations: first script to run, expected screen view, tuning order, UART/log examples, symptom table, final motion acceptance test.
- File-aware routing: when files are available, read `dist/codex/maixcam-master/references/maixcam-vision-workflow.md` and `dist/codex/maixcam-master/references/maixpy-api-map.md` as needed.

Expected: a user can paste `PROMPT.md` into a chat AI and then ask for a MaixCAM vision task without needing Codex.

### Task 3: Add Agent Adapter Files

**Files:**
- Create: `AGENTS.md`
- Create: `CLAUDE.md`
- Create: `GEMINI.md`

- [ ] **Step 1: Write `AGENTS.md`**

Include instructions for generic coding agents:

- Read `PROMPT.md` first.
- Use the reference files under `dist/codex/maixcam-master/references/` only when the task needs staged workflow details or MaixPy API patterns.
- Preserve the screen-first, touch-first, staged-validation behavior.
- Verify uncertain MaixPy APIs before claiming exact code.

Expected: agents that support root instruction files can load the package without installing the Codex skill.

- [ ] **Step 2: Write `CLAUDE.md`**

Include a thin Claude Code adapter:

- Treat `PROMPT.md` as the primary project instruction.
- Read references only when needed.
- Avoid duplicating the full prompt body.

Expected: Claude Code users can clone the repository and receive the same MaixCAM behavior from the root file.

- [ ] **Step 3: Write `GEMINI.md`**

Include a thin Gemini CLI adapter with the same routing behavior as `CLAUDE.md`.

Expected: Gemini CLI users can clone the repository and receive the same MaixCAM behavior from the root file.

### Task 4: Rewrite Repository Docs

**Files:**
- Modify: `README.md`
- Create: `docs/install.md`
- Create: `docs/usage.md`
- Create: `docs/platform-support.md`
- Create: `docs/ai-agent-support.md`

- [ ] **Step 1: Rewrite `README.md`**

Cover:

- What the package is.
- Quick decision table for `PROMPT.md`, `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, and Codex skill installation.
- Short Chinese and English example prompts.
- Links to docs.

Expected: a new user can decide which entry file to use in under one minute.

- [ ] **Step 2: Write `docs/install.md`**

Include exact install commands:

```bash
mkdir -p "$HOME/.codex/skills"
cp -R dist/codex/maixcam-master "$HOME/.codex/skills/maixcam-master"
```

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -Recurse -Force ".\dist\codex\maixcam-master" "$env:USERPROFILE\.codex\skills\maixcam-master"
```

Expected: macOS, Linux, and Windows users all have a concrete install path.

- [ ] **Step 3: Write `docs/usage.md`**

Explain:

- Copy `PROMPT.md` for any normal chat AI.
- Keep the repository root open for agents that read `AGENTS.md`, `CLAUDE.md`, or `GEMINI.md`.
- Install `dist/codex/maixcam-master/` for Codex skill invocation.

Expected: usage is clear without requiring users to understand Codex internals.

- [ ] **Step 4: Write `docs/platform-support.md`**

State:

- macOS, Linux, and Windows are supported as host platforms for editing and skill installation.
- MaixCAM hardware behavior depends on target firmware and must be verified on device.
- MaixPy API uncertainty should be resolved with official Sipeed docs or source.

Expected: users do not confuse host OS support with hardware API validation.

- [ ] **Step 5: Write `docs/ai-agent-support.md`**

Map:

- Any chat AI to `PROMPT.md`.
- Generic agents and Codex repository work to `AGENTS.md`.
- Claude Code to `CLAUDE.md`.
- Gemini CLI to `GEMINI.md`.
- Codex installable skill to `dist/codex/maixcam-master/`.

Expected: each AI surface has one obvious entry point.

### Task 5: Clean Platform Artifacts

**Files:**
- Create: `.gitignore`
- Remove: `.DS_Store`

- [ ] **Step 1: Add `.gitignore`**

Write:

```gitignore
.DS_Store
```

Expected: future macOS metadata files stay out of git status.

- [ ] **Step 2: Remove current `.DS_Store` files**

Run:

```bash
rm -f .DS_Store
```

Expected: `.DS_Store` no longer appears in `git status --short`.

### Task 6: Validate

**Files:**
- Inspect: all files changed in this plan.

- [ ] **Step 1: Check tree shape**

Run:

```bash
find . -maxdepth 4 -type f | sort
```

Expected: root entry files, `docs/`, and `dist/codex/maixcam-master/` are visible.

- [ ] **Step 2: Check for unfinished markers**

Run:

```bash
rg -n "(TO''DO|TB''D|FIX''ME|\\?\\?)" README.md PROMPT.md AGENTS.md CLAUDE.md GEMINI.md docs dist/codex/maixcam-master
```

Expected: no matches.

- [ ] **Step 3: Validate Codex skill frontmatter**

Run:

```bash
sed -n '1,20p' dist/codex/maixcam-master/SKILL.md
```

Expected: YAML frontmatter contains `name: maixcam-master` and a clear `description:`.

- [ ] **Step 4: Check git status**

Run:

```bash
git status --short --branch
```

Expected: changes only reflect the planned restructure, docs, root adapters, and `.gitignore`.
