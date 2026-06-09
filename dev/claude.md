# Claude

## settings.json

Project-level configuration file for Claude Code. Defines permissions, environment variables, and hooks that apply to all team members working in the repository.

### Settings Hierarchy

Claude Code applies settings in order, with lower levels overriding higher ones:

| Level | Path | Scope |
|---|---|---|
| Global (User) | `~/.claude/settings.json` | Applies to all projects |
| Project | `.claude/settings.json` | Shared with the team, committed to git |
| Project Local | `.claude/settings.local.json` | Personal only, excluded from git |

### Purpose

`.claude/settings.json` is committed to the repository and shared across the team. Use it to standardize tool permissions and hooks for everyone working in the project.

- Define which tools and commands Claude is allowed to run without prompting
- Set up shared hooks (e.g., linters, formatters) that run automatically
- Use `settings.local.json` for personal or machine-specific overrides on top of this file

### Structure

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git *)"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  },
  "env": {
    "MY_API_KEY": "local-value",
    "DEBUG": "true"
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'tool used'"
          }
        ]
      }
    ]
  }
}
```

### Key Fields

- **`permissions.allow`** — List of tool patterns to auto-approve
- **`permissions.deny`** — List of tool patterns to always block
- **`env`** — Environment variables injected into the Claude Code session
- **`hooks`** — Shell commands to run before/after tool calls

### Permissions Pattern

Specify allowed or denied commands using the `Bash(pattern)` format.

```json
"allow": [
  "Bash(pytest *)",
  "Bash(python *)",
  "Read",
  "Edit"
]
```

Glob patterns are supported. Using just the tool name (e.g., `"Read"`) allows the entire tool unconditionally.

### Hooks

Attach shell commands to specific Claude Code lifecycle events.

| Event | Timing |
|---|---|
| `PreToolUse` | Before a tool is called |
| `PostToolUse` | After a tool returns |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes a response |

```json
"hooks": {
  "Stop": [
    {
      "hooks": [
        {
          "type": "command",
          "command": "notify-send 'Claude finished'"
        }
      ]
    }
  ]
}
```

### Recommended .gitignore

```
.claude/settings.local.json
```

### Reference

- [Claude Code Settings](https://docs.anthropic.com/en/docs/claude-code/settings)
