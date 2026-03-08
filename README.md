# twicc-tmux-presets

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) for [TwiCC](https://github.com/twidi/twicc) (the web interface for Claude Code).

## Installation

1. Add the marketplace:
```
/plugin marketplace add dguerizec/twicc-tmux-presets
```

2. Install the plugin:
```
/plugin install twicc-presets@twicc-tmux-presets
```

## Available plugins

### twicc-presets

Create, edit, validate, and manage `.twicc-tmux.json` files — the terminal preset configuration for TwiCC's tmux-based terminal navigator.

#### Usage

```
/twicc-presets:twicc-presets create presets for dev server, tests, and logs
/twicc-presets:twicc-presets add a "deploy" preset that runs ./deploy.sh
/twicc-presets:twicc-presets list
/twicc-presets:twicc-presets validate
```

Or just ask naturally — Claude auto-invokes the skill when you mention twicc presets or `.twicc-tmux.json`.

#### `.twicc-tmux.json` format

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
  },
  {
    "name": "shell"
  }
]
```

| Field     | Required | Description |
|-----------|----------|-------------|
| `name`    | Yes      | Preset name shown in the TwiCC terminal navigator |
| `command` | No       | Shell command to run when the window is created |
| `cwd`     | No       | Working directory (relative to the config file's directory, or absolute) |

## License

MIT
