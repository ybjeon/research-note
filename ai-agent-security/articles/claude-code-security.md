# Claude Code Security

Language: en
Source: https://code.claude.com/docs/en/security
Date: 2026-06-29

## Overview

Claude Code is built with a permission-based security model that defaults to read-only access and requires explicit user approval for any system-modifying actions. It includes protections against prompt injection, MCP abuse, and credential exposure.

## Key Points

- Read-only by default; file edits, command execution, and other mutations require explicit user approval
- Write access is confined to the directory where Claude Code was started (cannot write to parent directories)
- Built-in allowlist for safe read-only commands (`ls`, `cat`, `git status`) that run without prompting
- Sandbox mode (`/sandbox`) adds filesystem and network isolation for autonomous work
- Accept Edits mode auto-approves file edits and a fixed set of safe filesystem commands within the working directory
- Network commands (`curl`, `wget`) are not auto-approved and must be explicitly allowed
- API keys and tokens are stored in the macOS Keychain (file-permission-protected on Windows/Linux)
- MCP servers require trust verification on first run; Anthropic lists but does not security-audit third-party servers
- Cloud sessions run in isolated Anthropic-managed VMs with audit logging and automatic cleanup
- Remote Control sessions run locally with all execution staying on the user's machine, transmitted over TLS

## Details

**Prompt Injection Protections**
- Context-aware analysis detects potentially malicious instructions
- Input sanitization prevents command injection
- Web fetch uses an isolated context window to avoid injecting untrusted content into the main session
- Suspicious bash commands require manual approval even if previously allowlisted (fail-closed matching)

**Team and Organizational Controls**
- Managed settings files enforce org-wide standards via version control
- OpenTelemetry metrics available for usage monitoring
- `ConfigChange` hooks can audit or block settings changes during a session

**Best Practices Recommended by Anthropic**
- Review all suggested commands before approval
- Avoid piping untrusted content directly to Claude
- Use dev containers or VMs when interacting with external services
- Use project-specific permission settings for sensitive repos

## Conclusion

Claude Code's security model is defense-in-depth: default read-only permissions, sandboxed execution, explicit approval flows, and fail-closed defaults. Users remain responsible for reviewing proposed changes. Report vulnerabilities via the HackerOne program.

## Reference

- [Anthropic Trust Center](https://trust.anthropic.com)
- [Permissions](https://code.claude.com/docs/en/permissions)
- [Sandboxing](https://code.claude.com/docs/en/sandboxing)
- [Sandbox Environments](https://code.claude.com/docs/en/sandbox-environments)
- [Development Containers](https://code.claude.com/docs/en/devcontainer)
- [Monitoring Usage](https://code.claude.com/docs/en/monitoring-usage)
- [Claude Code on the Web](https://code.claude.com/docs/en/claude-code-on-the-web)
- [VS Code Security and Privacy](https://code.claude.com/docs/en/vs-code#security-and-privacy)
- [HackerOne Vulnerability Report](https://hackerone.com/4f1f16ba-10d3-4d09-9ecc-c721aad90f24/embedded_submissions/new)
