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

## Guardrails — MUST follow for all work in this repo

### Scope discipline
- Do NOT add features, commands, sections, or files not specified in the chunk.
- Do NOT refactor or reorganize the plugin structure beyond what the current chunk requires.
- Do NOT add "nice to have" improvements like config files, environment variable support, or user preferences.
- Do NOT create a build system, test suite, linting config, or CI pipeline. This is a Markdown-only plugin.
- If you think something additional is needed, state it and wait for approval. Do not implement speculatively.

### Format discipline
- The Verum spec format has exactly these canonical sections: Stories, Accepts, Produces, Behaviors, Boundaries, Invariants, Depends on / Exposes to, Decisions.
- Do NOT add sections. No "Overview", no "Architecture", no "API Reference", no "Testing", no "Performance", no "Security", no "Future Work", no "TODO".
- Do NOT add YAML frontmatter to spec files. Section headings are the schema.
- Do NOT use code blocks with language-specific type notation in templates or examples. Use natural language with parenthetical hints: `(required, text)`, `(optional, list)`.
- The top-level description uses `>` blockquote syntax. Not a paragraph, not a heading.

### File discipline
- Only create or modify files listed in the chunk's "Involved areas/files" section.
- Do NOT touch files from previous chunks unless the current chunk explicitly says to.
- Do NOT create files outside the `verum-plugin/` directory.
- Do NOT create hidden files (`.verum`, `.verumrc`, `.verum.json`) — the plugin uses `plugin.json` and that's it.

### Command discipline
- Slash commands are Markdown files with natural language prompts. They are NOT scripts.
- Do NOT embed code execution logic in commands — use `allowed-tools` frontmatter to grant tool access, then describe the workflow in natural language.
- Do NOT create wrapper scripts, helper functions, or utility modules for commands to call.
- Every command must handle the case where `verum/` doesn't exist — either suggest `/verum-init` or warn the user.

### Style discipline
- All documentation and specs use plain, direct language. No corporate jargon, no hedging.
- Do NOT use emoji in spec files or documentation.
- Heading levels: `#` for component name, `##` for sections, `###` for subsections (stories, behaviors).
- Cross-references use relative Markdown links: `[Component](component-name.md)`.
- Decisions use bold date prefix: `**YYYY-MM-DD: Decision title.**`

### Behavioral discipline
- After each chunk, verify the completion criteria before moving to the next.
- If a chunk's instruction is ambiguous, ask for clarification. Do NOT resolve ambiguity by inventing a solution.
- Do NOT combine chunks or skip ahead. Execute in order.
- Commit after each completed chunk with message format: `verum: chunk N — [short name]`.
