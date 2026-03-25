# Verum — Claude Code Plugin

## What this repo is

Verum is a pure-Markdown Claude Code plugin. There is no build step, no package.json, no compiled code. The plugin lives entirely in `verum-plugin/` and consists of Markdown files, JSON manifests, and directory conventions.

## Project structure

- `verum-plugin/.claude-plugin/plugin.json` — plugin manifest (name, version, metadata)
- `verum-plugin/commands/` — slash command definitions (*.md files)
- `verum-plugin/skills/` — reusable skill definitions (skill-name/SKILL.md)
- `verum-plugin/agents/` — agent definitions (*.md files)
- `verum-plugin/hooks/` — hook configuration and scripts

## Development conventions

- Load the plugin with `claude --plugin-dir ./verum-plugin` during development.
- Commands are Markdown files with YAML frontmatter in `commands/`.
- Skills are directories under `skills/` containing a `SKILL.md` file.
- Agents are Markdown files with YAML frontmatter in `agents/`.
- Hooks are configured in `hooks/hooks.json`.
- Do not add npm dependencies, build steps, or a package.json. This is a pure-Markdown plugin.

## Current state

v0.1.0 — skeleton only. No functional commands, skills, agents, or hooks exist yet.
