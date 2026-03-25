# Verum

Verum is a Claude Code plugin that provides structured verification and validation for AI-assisted development workflows. It aims to bring systematic checks, assertions, and traceability to the work Claude Code performs — ensuring that outputs meet expectations before they're accepted.

## Status

v0.1.0 — Plugin skeleton only. No commands, skills, or hooks are implemented yet.

## Installation

**Development (local):**

```sh
claude --plugin-dir ./verum-plugin
```

**Release (future):**

Verum will be distributed through the Claude Code plugin marketplace once it reaches a stable release.

## Structure

```
verum-plugin/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest
├── commands/             # Slash commands (empty)
├── skills/               # Reusable skills (empty)
├── agents/               # Agent definitions (empty)
└── hooks/                # Hook handlers (empty)
```

## License

MIT
