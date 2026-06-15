# Claude Code `.claude/` Folder Reference
updated: 2026-06-14

## Structure Overview

```
project/.claude/          ← shared with team, git commit
├── CLAUDE.md              # project persistent memory (loaded every session)
├── settings.json          # shared settings (permissions, hooks, MCP)
├── settings.local.json    # local-only overrides (gitignore)
├── skills/                # context-triggered auto-loaded workflows
├── commands/              # manual slash commands (legacy → merging into skills)
├── agents/                # context-isolated subagents
├── rules/                 # path-scoped additional rule files
├── hooks/                 # lifecycle event scripts
└── plans/                 # Plan Mode outputs

~/.claude/                 ← personal only, never git commit
├── CLAUDE.md              # global personal rules
├── settings.json          # personal global settings
├── commands/              # personal slash commands (shared across all projects)
├── skills/                # personal skills
├── agents/                # personal subagents
└── projects/              # session history & auto-memory
    └── -Users-me-myproject/
        ├── sessions/
        │   ├── abc123.jsonl        # complete session transcript
        │   └── sessions-index.json # metadata (summaries, git branch, timestamps)
        └── memory/
            └── MEMORY.md           # memory written automatically by Claude
```

### Folder Comparison at a Glance

| Folder | When Loaded | Who Invokes | Git Commit |
|--------|-------------|-------------|------------|
| `skills/` | Auto on relevant task detection | Claude auto or `/name` | o |
| `commands/` | When user calls `/name` | User | o |
| `agents/` | Parent spawns via Task tool | Claude auto or `@name` | o |
| `rules/` | Session start (path-scoped: only when working on matching files) | Auto | o |
| `hooks/` | On lifecycle event | Auto (deterministic) | o |
| `plans/` | Saved after Plan Mode completes | During Plan Mode | x recommended |
| `projects/` | — (storage) | — | x |

---

## 1. `skills/`

Reusable workflows that Claude loads automatically when a relevant task is detected. Unlike CLAUDE.md which is always loaded every session, skills are loaded **only when needed**, saving context.

### File Structure

```
.claude/skills/
└── security-review/
    ├── SKILL.md         ← required (frontmatter + instructions)
    ├── checklist.md     ← optional support file (can be @referenced)
    └── scan.sh          ← optional script
```

### SKILL.md Format

```yaml
---
name: security-review
description: >
  Run a security review on code changes. Use when reviewing auth,
  API endpoints, or data handling code. Triggers on: "security check",
  "review for vulnerabilities", "auth code review".
allowed-tools: Bash, Read, Grep
disable-model-invocation: false   # true = user must manually call /name
---

# Security Review Procedure

1. Check changes with `git diff`
2. Review authentication/authorization logic
3. Scan for SQL Injection, XSS vulnerabilities
4. Check dependency vulnerabilities

@checklist.md  ← inline reference to support file
```

### Key Points

- **description = trigger**: Not just a description — the string Claude uses for fuzzy matching to decide whether to activate. Vague descriptions result in silent non-activation
- **At startup, only name + description are scanned** (~100 tokens/skill), full load on match
- `disable-model-invocation: true`: Prevents Claude from auto-running workflows with side effects like `/deploy`, `/commit`
- `user-invocable: false`: Only Claude can call internally (for background knowledge)
- **Open standard**: Common format shared by Claude Code, OpenAI Codex CLI, Cursor, Gemini CLI, etc.

### skills vs commands Decision Guide

| | skills | commands |
|-|--------|----------|
| Trigger | Context-based auto | User explicitly calls `/name` |
| Purpose | Procedural knowledge of "how we do XYZ" | Actions to run at a specific time |
| Complexity | Can include support files, scripts | Simple prompt templates |

> **SKILL.md** in root folder?  
> Technically possible but not recommended — it would be loaded every session, consuming context without a clear trigger. Best practice is to put it in a named subfolder to ensure it's only loaded when relevant.

---

## 2. `commands/`

Prompt templates invoked with `/command-name`. Since v2.1.101, integrated with skills to share the same `.md` file format — new workflows are recommended to use the skills approach.

### File Structure

```
.claude/commands/
├── fix-issue.md              → /fix-issue
└── frontend/
    └── component.md          → /frontend:component  (namespacing)
```

### Command File Format

```yaml
---
description: Fix a GitHub issue by number
argument-hint: <issue-number>
allowed-tools: Bash, Read, Edit
model: claude-sonnet-4-6
---

Fix GitHub issue #$ARGUMENTS following our coding standards.

1. Read the issue description
2. Understand the requirements
3. Implement the fix
4. Write tests
5. Create a commit
```

### Argument Handling

```
/fix-issue 1234
           └─ $ARGUMENTS = "1234"

/deploy main production
        └─ $ARGUMENTS = "main production"
           $1 = "main"
           $2 = "production"
```

### Key Frontmatter Fields

| Field | Description |
|-------|-------------|
| `description` | One-line description shown in slash command picker |
| `argument-hint` | Placeholder shown in the input field (cosmetic) |
| `allowed-tools` | List of tools available in this command |
| `model` | Pin a specific model (useful to prevent version drift) |
| `disable-model-invocation` | `true` = Claude cannot auto-run, user-only invocation |

---

## 3. `agents/`

Specialized Claude instances with independent context windows. Isolates side tasks when the parent session context grows large, returning only a summary.

### File Structure

```
.claude/agents/
├── code-reviewer.md
├── test-writer.md
└── docs-generator.md
```

### Agent File Format

```yaml
---
name: code-reviewer
description: >
  Expert code review specialist. Use after writing or modifying code
  to check quality, security, and maintainability.
  Trigger: "review this code", "check for security issues".
tools: Read, Grep, Glob, Bash
model: claude-haiku-4-5   # haiku for fast tasks, opus for complex reasoning
---

You are a senior code reviewer. When invoked:

1. Run `git diff` to see recent changes
2. Focus on modified files
3. Check for security vulnerabilities
4. Review code quality and maintainability
5. Return a structured summary with findings
```

### Key Points

- **description = trigger**: Determines which subagent the parent agent spawns. Write clearly in the form "Use this agent when X"
- **Independent context**: Each subagent has its own context window, preventing parent session contamination
- **Permission restriction is important**: Subagents cannot show permission prompts to users. `ask` rule matches result in automatic denial — recommend read-only tool sets. Delegate Edit/Write to the parent
- **Model override**: `CLAUDE_CODE_SUBAGENT_MODEL` env var forces a model for all subagents
- **Parallel execution**: Parent can spawn multiple subagents simultaneously to parallelize independent tasks

### skills vs agents Decision Guide

| | skills | agents |
|-|--------|--------|
| Purpose | Procedural knowledge (how) | Specialized roles |
| Context | Shared with parent session | Fully isolated independent window |
| Best for | Automating repetitive workflows | Large-scale exploration, parallel tasks |

---

## 4. `rules/`

Rule files that split out concerns when CLAUDE.md grows too long. Path-scoped rules are only loaded when working on matching files, saving context.

### File Structure

```
.claude/rules/
├── tests.md            ← always loaded (no path scope)
├── python-types.md     ← always loaded
└── migrations.md       ← loaded only when working on .sql files
```

### Rule File Format

```yaml
# migrations.md (path scope example)
---
paths: ["**/*.sql", "**/migrations/**"]
---

# Migration Rules

- Always write reversible migrations (include DOWN migration)
- Use batch processing for large table changes
- Create indexes with CONCURRENTLY option
- Design multi-phase migrations for zero-downtime deployments
```

```yaml
# tests.md (always loaded, no path scope)
---
# frontmatter optional
---

# Test Rules

- Unit tests required for every new function
- External dependencies must be mocked
- Test file naming: {module}.test.ts
```

### CLAUDE.md vs rules Comparison

| | CLAUDE.md | rules/ |
|-|-----------|--------|
| When Loaded | Always (every session) | Always or on path match |
| Context Savings | ❌ always consumed | ✅ saved with path scope |
| Purpose | Global architecture/conventions | Domain-specific detailed rules |
| Recommended Size | Under 200 lines | Short per file |

> ⚠️ Importing files with `@import` does NOT save context — they are all loaded at startup anyway. Real context savings are only possible with path-scoped rules.

---

## 5. `hooks/`

Scripts that run automatically on Claude Code lifecycle events. Unlike "requests" in CLAUDE.md, these are **guaranteed execution** — they run regardless of Claude's judgment.

### Configuration in settings.json

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [{
          "type": "command",
          "command": "pnpm lint --fix",
          "timeout": 30
        }]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{
          "type": "command",
          "command": "bash .claude/hooks/block-secrets.sh"
        }]
      }
    ],
    "Stop": [
      {
        "matcher": "*",
        "hooks": [{
          "type": "command",
          "command": "node .claude/hooks/notify-slack.js",
          "async": true
        }]
      }
    ]
  }
}
```

### Key Events

| Event | Timing | Primary Use |
|-------|--------|-------------|
| `PreToolUse` | Just before tool execution | Block/allow decisions, security gates |
| `PostToolUse` | Just after tool execution | Auto-formatting, validation |
| `Stop` | When Claude response completes | Notifications, post-completion checks |
| `SessionStart` | On session start/resume/clear/compact | Environment initialization |
| `SessionEnd` | On session end | Backup, logging |
| `UserPromptSubmit` | When user submits a message | Input preprocessing |
| `SubagentStop` | When subagent completes | Subagent result validation |

### Exit Code Semantics (PreToolUse)

```
exit 0  → allow (continue)
exit 2  → block (stderr message is passed to Claude)
```

### Handler Types

| Type | Description |
|------|-------------|
| `command` | Run a shell script |
| `http` | POST to an endpoint (added February 2026) |
| `prompt` | Single-turn LLM evaluation |
| `agent` | Subagent with tool access |

Add `async: true` for non-blocking background execution (notifications, logging).

### CLAUDE.md vs hooks Decision Guide

- **Needs judgment** → CLAUDE.md ("fix ESLint errors if any")
- **Must run without exception** → hooks (`PostToolUse` to always auto-run lint)
- Having both is a conflict signal — if a hook auto-runs something and CLAUDE.md has the same instruction, Claude will attempt it redundantly

---

## 6. `plans/`

Markdown execution plan files written by Claude in Plan Mode.

### Default Behavior

- On entering Plan Mode, Claude writes the planned actions as markdown before making changes
- Default save location: `~/.claude/plans/` (global, mixed across all projects)
- File names: auto-generated like `jaunty-petting-nebula.md`

### Per-Project Separation (Recommended)

```json
// .claude/settings.json
{
  "plansDirectory": ".claude/plans"
}
```

```
project/.claude/plans/
├── refactor-auth-2026-06.md
└── add-payment-module.md
```

### Example Plan File

```markdown
# Refactor Authentication Module

## Goal
Migrate to JWT-based authentication, remove session store

## Steps
1. [ ] Read `auth/session.ts` — understand current structure
2. [ ] Write JWT utility functions (`auth/jwt.ts`)
3. [ ] Replace middleware (`middleware/auth.ts`)
4. [ ] Update existing session-related tests
5. [ ] Run integration tests

## Affected Files
- `src/auth/session.ts`
- `src/middleware/auth.ts`
- `tests/auth.test.ts`
```

### Plan Mode in VS Code

When entering Plan Mode, Claude automatically opens the plan as a full markdown document. Add inline comments as feedback and approve to start execution.

---

## 7. `projects/` (`~/.claude/projects/`)

Complete conversation history for all sessions and memory written automatically by Claude. Personal only — never commit to git.

### Structure

```
~/.claude/projects/
└── -Users-me-myproject/          ← absolute path converted to -
    ├── sessions/
    │   ├── abc123.jsonl          ← complete session transcript (JSONL)
    │   └── sessions-index.json   ← session metadata
    └── memory/
        └── MEMORY.md             ← auto-memory (written by Claude)
```

### sessions/*.jsonl Contents

Not just simple logs — complete records:
- Exact input/output of tool calls
- Extended thinking blocks
- Subagent spawn events
- Per-turn token usage
- Model selection, working directory, git state snapshot

### sessions-index.json Contents

```json
{
  "sessions": [
    {
      "id": "abc123",
      "summary": "JWT authentication module refactoring",
      "messageCount": 42,
      "gitBranch": "feat/jwt-auth",
      "createdAt": "2026-06-10T09:00:00Z",
      "modifiedAt": "2026-06-10T11:30:00Z"
    }
  ]
}
```

### auto-memory (MEMORY.md)

Claude automatically records during sessions:
- Discovered commands (e.g., `pnpm test`)
- Observed patterns and architecture insights
- Corrected preferences

View and edit with `/memory` command. To clear, delete the file — Claude Code will recreate it.

### Session Resume Commands

```bash
claude -c                  # resume most recent session for current project
claude -r SESSION_ID       # resume a specific session by ID
/history                   # list recent sessions from within a session
```

---

## Recommended Setup Example

```
myproject/
├── CLAUDE.md              ← architecture, key conventions (under 200 lines)
└── .claude/
    ├── settings.json      ← hooks, permissions, plansDirectory
    ├── rules/
    │   ├── tests.md       ← test rules (always loaded)
    │   └── migrations.md  ← DB rules (loaded only for *.sql)
    ├── skills/
    │   ├── security-review/SKILL.md
    │   └── pr-checklist/SKILL.md
    ├── agents/
    │   ├── code-reviewer.md
    │   └── test-writer.md
    ├── commands/
    │   └── fix-issue.md   ← /fix-issue (legacy or manual-only invocation)
    └── plans/             ← saved here when plansDirectory is configured
```

```json
// .claude/settings.json
{
  "plansDirectory": ".claude/plans",
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [{ "type": "command", "command": "pnpm lint --fix", "timeout": 30 }]
      }
    ]
  }
}
```

---

## References

- [Claude Code Official Memory Guide](https://code.claude.com/docs/en/memory)
- [Claude Code Official Slash Commands Documentation](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Features & Settings Reference 2026](https://hidekazu-konishi.com/entry/claude_code_features_settings_reference_2026.html)
- [Claude Code Hooks Complete Guide](https://claudefa.st/blog/tools/hooks/hooks-guide)
- [Claude Code Subagents Guide](https://www.tembo.io/blog/claude-code-subagents)
- [Anatomy of the .claude Folder (codewithmukesh)](https://codewithmukesh.com/blog/anatomy-of-the-claude-folder/)
- [SKILL.md Spec: Every Field](https://www.agensi.io/learn/skill-md-format-reference)
