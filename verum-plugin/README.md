# Verum

Living behavioral specifications that accrete during development. Verum maintains a structured description of what your system currently does — not what it should do, not what it used to do. Specs are created once, then updated automatically as Claude Code makes behavioral changes to your code.

## Installation

Development (from this repo):

```sh
claude --plugin-dir ./verum-plugin
```

Persistent install:

```sh
cp -r verum-plugin ~/.claude/plugins/verum
```

## Quick start

1. Run `/verum-init` in any project with source code.
2. Review the generated specs in `verum/`.
3. Start building. The `verum-update` skill automatically updates specs when you make behavioral changes.
4. Run `/verum` periodically to catch any drift the auto-update missed.

## Commands

### /verum-init

Scaffold the `verum/` directory and generate seed specs from codebase analysis.

```
/verum-init
```

Creates `verum/index.md`, component stubs in `verum/components/`, and empty `invariants.md` and `decisions.md`. Safe to run — refuses to overwrite if `verum/` already exists.

### /verum

Review specs against recent code changes and propose surgical updates.

```
/verum                    # review all components
/verum task-router        # review one component
/verum behaviors          # review one section across all components
```

Shows what the spec says vs. what the code does, proposes specific edits, and asks before applying.

### /verum-consult

Consult specs before building a feature. Read-only — never modifies files.

```
/verum-consult add priority scheduling to the task router
```

Produces a structured report: affected components, relevant behaviors, boundaries to respect, invariants at risk, past decisions, suggested approach, and which specs will need updating.

## Spec format

Every component spec has exactly these sections — no more, no fewer:

| Section | What belongs there |
|---|---|
| **Stories** | Narrative paragraphs describing why this component exists |
| **Accepts** | Every input: parameters, config values, user actions |
| **Produces** | Every output: return values, events, side effects |
| **Behaviors** | Named scenarios — what happens in specific situations |
| **Boundaries** | Hard limits, rejection conditions, edge cases |
| **Invariants** | Falsifiable properties that must always hold |
| **Depends on** | Components this one requires |
| **Exposes to** | Components that depend on this one |
| **Decisions** | Implementation choices with dates and reasoning |

Top-level description uses `>` blockquote. Inputs use `- **name** (required/optional, type hint) — description`. Cross-references use relative Markdown links. Full format reference in `templates/component.md`.

## How it works

Three mechanisms keep specs current:

1. **Auto-update skill** — the `verum-update` skill detects behavioral code changes and applies surgical spec updates in the same commit. Triggers on interface changes, new behaviors, boundary changes, and dependency shifts. Ignores formatting, tests, docs, and dependency bumps.

2. **Stop hook** — a lightweight prompt hook runs at session end. If behavioral code changed but `verum/` wasn't touched, it reminds you to run `/verum`.

3. **Manual review** — `/verum` compares specs against recent git changes and proposes section-level edits. You confirm before anything is applied.

## What Verum is NOT

- Not a test framework — specs describe behavior, they don't verify it.
- Not a code generator — specs don't produce code, code produces specs.
- Not a requirements tool — specs reflect what IS, not what SHOULD BE.
- Not a substitute for code comments — specs describe component-level behavior, not line-level intent.

## Limitations

v0.1 — early and rough:

- Auto-update is probabilistic. The skill triggers on ~80% of behavioral changes. Run `/verum` to catch the rest.
- No formal validation that specs match code. Specs are maintained by convention, not enforcement.
- No multi-session conflict resolution. If two sessions change the same component, the second writer wins.
- Component discovery during `/verum-init` is approximate. Review and adjust generated stubs.

## License

MIT
