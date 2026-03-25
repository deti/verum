# Verum

Structured verification and validation framework for Claude Code workflows.

## Overview

Verum is a Claude Code plugin that brings systematic checks, assertions, and traceability to AI-assisted development. It ensures that outputs meet expectations before they're accepted.

**Current status:** v0.1.0 — plugin skeleton only. The directory structure and manifest are in place; no commands, skills, or hooks are implemented yet.

## Getting Started

Load the plugin locally for development:

```sh
claude --plugin-dir ./verum-plugin
```

## Repository Structure

```
verum/
├── LICENSE
├── README.md
├── CLAUDE.md
└── verum-plugin/           # Claude Code plugin
    ├── .claude-plugin/
    │   └── plugin.json
    ├── README.md
    ├── commands/
    ├── skills/
    ├── agents/
    └── hooks/
```

## License

MIT — see [LICENSE](LICENSE).
