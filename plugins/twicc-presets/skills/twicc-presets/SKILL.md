---
name: twicc-presets
description: "MUST invoke BEFORE writing any .twicc-tmux.json file. The format is NOT obvious and WILL be wrong without this skill. Use whenever: creating/editing/reading .twicc-tmux.json, user mentions twicc presets, terminal presets, tmux presets, preset file, or configuring shell presets for TwiCC."
argument-hint: "[action] [args...]"
---

# TwiCC Terminal Presets Manager

**IMPORTANT: You MUST load this skill BEFORE writing or editing any `.twicc-tmux.json` file. The format is a specific JSON structure that you will get wrong without reading these instructions. Do NOT guess the format.**

Manage `.twicc-tmux.json` files that configure terminal shell presets in [TwiCC](https://github.com/twidi/twicc) (the web interface for Claude Code).

## File format

A `.twicc-tmux.json` file is a **JSON array** at the root (NOT an object). Each entry is a preset:

```json
[
  {
    "name": "dev",
    "command": "npm run dev",
    "cwd": "./frontend"
  },
  {
    "name": "logs",
    "command": "tail -f logs/app.log"
  }
]
```

### Fields

| Field     | Required | Description |
|-----------|----------|-------------|
| `name`    | Yes      | Preset name, displayed in the TwiCC terminal navigator |
| `command` | No       | Shell command to run when the preset window is created |
| `cwd`     | No       | Working directory. Relative paths resolve against the directory containing the `.twicc-tmux.json` file. Defaults to that directory if omitted. |

### CRITICAL format rules

- The root **MUST** be a JSON array `[...]`. **NEVER** use an object `{...}` as root.
- The **ONLY** allowed keys per entry are: `name`, `command`, `cwd`. No other keys.
- Each entry **MUST** have a `name` field (string, non-empty).
- The `command` field is optional. If present, it is sent as keystrokes to the tmux window after creation.
- The `cwd` field is optional. Relative paths are resolved against the directory containing the `.twicc-tmux.json`. If omitted, the preset opens in the directory containing the config file.
- Preset names should be descriptive and concise (shown in the UI as buttons).

### WRONG — do NOT generate these formats

```json
// WRONG: root is an object
{"name": "myproject", "shells": [{"name": "dev", "command": "..."}]}

// WRONG: uses "shells", "windows", or any wrapper key
{"windows": [{"name": "dev"}]}

// WRONG: extra keys like "root", "focus", "env", "shell"
[{"name": "dev", "focus": true, "env": {"FOO": "bar"}}]
```

### CORRECT — always use this exact structure

```json
[
  {"name": "preset-name", "command": "optional command", "cwd": "optional/path"}
]
```

## Multi-source preset resolution

TwiCC resolves presets from up to 3 sources when opening a terminal session:

1. **Project directory** — the project root where Claude Code was launched
2. **Git root** — if different from the project directory
3. **Session CWD walk-up** — walks parent directories from the session's current working directory until it finds a `.twicc-tmux.json` (stops at project or git root boundary)

Each source that has a `.twicc-tmux.json` appears as a separate labeled section in the terminal navigator. This means you can have:
- A project-level preset file for global commands (build, test, deploy)
- Sub-directory preset files for module-specific commands (e.g., `frontend/.twicc-tmux.json` for frontend dev scripts)

## Actions

Based on the user's request, perform one of these actions:

### Create a new preset file

1. Ask the user which directory to place it in (default: current project root).
2. Ask what presets they want (names, commands, optional cwd).
3. Write the `.twicc-tmux.json` file as a valid JSON array.
4. Validate the file is valid JSON and follows the format rules.

### Add presets to an existing file

1. Read the existing `.twicc-tmux.json`.
2. Add the new preset(s) to the array.
3. Ensure no duplicate names.
4. Write the updated file.

### Edit or remove presets

1. Read the existing `.twicc-tmux.json`.
2. Apply the requested changes (rename, update command/cwd, or remove entries).
3. Write the updated file.

### List / show presets

1. Find all `.twicc-tmux.json` files in the current project (use `find` or `Glob`).
2. Display each file's location and its presets in a readable format.

### Validate a preset file

1. Read the file and check:
   - Root is a JSON array
   - Each entry has a non-empty `name` string
   - No duplicate names within the same file
   - `cwd` paths (if present) resolve to existing directories
2. Report any issues found.

## Tips for good presets

- **Use `exec` prefix** for one-shot commands that should replace the shell (e.g., `"exec uv run ./devctl.py restart"`). The tmux window closes automatically when the command finishes.
- **Omit `exec`** for long-running commands you want to interact with (e.g., `"tail -f logs/app.log"`).
- **Use relative `cwd`** when possible so the preset file works across machines.
- **Group related presets** in the same `.twicc-tmux.json` at the relevant directory level.
