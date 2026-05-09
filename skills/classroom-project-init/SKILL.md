---
name: classroom-project-init
description: Initialize a Codex classroom-tools workspace. Use when the user asks to create a classroom tools master folder, GitHub public repo, project blueprint, Obsidian work note, tools folder, or Codex startup/shutdown workflow for "開工" and "收工".
---

# Classroom Project Init

Use this skill to initialize a classroom-tools project that can be continued across Codex sessions.

## Workflow

1. Read the user-provided handout or brief first.
2. Confirm environment:
   - operating system
   - `git --version`
   - `gh auth status`
   - `node --version` when Firebase or frontend work is expected
3. Confirm the target project name and repo name. Prefer short lowercase GitHub names such as `yiping-tools`.
4. Confirm or discover the Obsidian vault path. On this machine the known vault path is:

```text
G:\我的雲端硬碟\secondbrain
```

5. Create the project blueprint in the repo root:
   - `CLAUDE.md` or a project blueprint file used by the user's workflow
   - `.gitignore`
   - `tools/<first-tool-name>/.gitkeep` when the first tool folder is requested
6. Use `.gitignore` to exclude local settings and secrets:

```text
.claude/
.codex/
.env
*.key
credentials.*
node_modules/
```

7. Create the Obsidian work note:

```text
<vault>/<repo-name>/工作筆記.md
```

8. Initialize Git:

```powershell
git init
git config windows.appendAtomically false
git add <specific files>
git commit -m "初始化班級工具總專案"
```

9. Create the public GitHub repo and push:

```powershell
gh repo create <repo-name> --public --source=. --push
```

10. Verify:
   - `git status --short` is clean
   - `gh repo view <owner>/<repo-name> --json name,url,visibility,defaultBranchRef`

## Codex 開工 / 收工 Skills

If the user asks for Codex skills, install them under:

```text
C:\Users\vm\.codex\skills\startup
C:\Users\vm\.codex\skills\shutdown
```

Do not install only to `C:\Users\vm\.claude-skills` unless the user explicitly wants Claude Code.

Codex skill names should use lowercase English folder names (`startup`, `shutdown`), while the descriptions should include Chinese triggers such as `開工`, `/開工`, `收工`, and `/收工`.

After installing or updating Codex skills:

```powershell
python -X utf8 C:\Users\vm\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\vm\.codex\skills\startup
python -X utf8 C:\Users\vm\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\vm\.codex\skills\shutdown
```

Tell the user to fully restart Codex so the skills reload.

## Safety

- Do not stage `.claude/`, `.codex/`, `.env`, keys, credentials, or token-bearing local settings.
- In Google Drive folders, set `git config windows.appendAtomically false` before committing.
- Check whether a GitHub repo already exists before creating it.
- Use specific `git add` paths rather than blindly staging unrelated user files.
