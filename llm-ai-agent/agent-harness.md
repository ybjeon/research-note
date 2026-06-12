# Harness in AI Agent

An **agent harness (에이전트 하네스)** is the surrounding infrastructure that configures, constrains, and instructs an AI coding agent at the project level. It typically consists of instruction files, permission settings, and skill definitions that the agent reads before acting.

---

## Instruction files (AGENTS.md / CLAUDE.md)

`AGENTS.md` is a Markdown instruction file placed in a project repository to provide persistent, project-level context to AI coding agents. It functions similarly to `CLAUDE.md` (Anthropic Claude Code) but is recognized by a broader set of agents.

### Supported Agents

| Agent | File recognized |
|---|---|
| **OpenAI Codex** | `AGENTS.md` |
| **OpenHands** | `AGENTS.md` |
| **Claude Code** | `CLAUDE.md` (primary), `AGENTS.md` (also read) |
| **Cursor Agent** | `.cursorrules` / `AGENTS.md` (partially) |

### Typical Contents

```markdown
# Project Agent Instructions

## Overview
Brief description of the project and its purpose.

## Architecture
- Key directories and what they contain
- Important design patterns in use

## Coding Conventions
- Language version, formatter, linter settings
- Naming conventions, file structure rules

## Testing
- How to run tests
- Which test framework is used

## Commands
- Build: `make build`
- Test: `pytest tests/`
- Lint: `ruff check .`

## Constraints
- Do not modify files under `generated/`
- Always run tests before committing
```

### Resolution Order

Agents typically merge instructions from multiple files in this priority order:

1. System prompt (agent's own defaults)
2. Root `AGENTS.md` / `CLAUDE.md`
3. Subdirectory `AGENTS.md` (closer to the working file takes precedence)

---

## SKILLS.md / skill files

Some harness implementations (e.g., Claude Code) extend the base instruction file with **skill definitions** — reusable, named procedures the agent can invoke. Skills encapsulate multi-step workflows (build, test, deploy, review) and can be triggered by slash commands.

```markdown
# Skill: run-tests
Runs the full test suite and reports failures.

Steps:
1. `pytest tests/ -v`
2. Parse output for FAILED lines
3. Report a summary to the user
```

---

## Reference

[1] [OpenAI Codex — AGENTS.md spec](https://platform.openai.com/docs/codex/agents-md)  
[2] [Claude Code — CLAUDE.md documentation](https://docs.anthropic.com/en/docs/claude-code/memory)  
