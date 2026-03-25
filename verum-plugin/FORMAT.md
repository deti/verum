# Verum Spec Format

## File organization

A Verum spec lives in a `verum/` directory at the project root:

```
verum/
  index.md              — project overview, component list, system invariants, architecture decisions
  invariants.md         — cross-cutting system invariants (Data Integrity, Communication, Identity and Access)
  decisions.md          — system-wide architecture decisions
  components/
    component-name.md   — one file per component
```

## Naming conventions

- Component spec files use lowercase hyphenated names: `task-router.md`, `worker-pool.md`.
- Cross-references use relative Markdown links with bare filenames for same-directory files: `[Worker Pool](worker-pool.md)`.
- Cross-references from `index.md` to components include the subdirectory: `[Worker Pool](components/worker-pool.md)`.

## index.md

```markdown
# Project Name

> One-line description of what this system does

## Components

- [Component Name](components/component-name.md) — what it does

## System Invariants

- Falsifiable statement that holds across all components

## Architecture Decisions

- **YYYY-MM-DD: Decision title.** Reasoning in 1-2 sentences.
```

`## System Invariants` holds invariants spanning multiple components. `## Architecture Decisions` holds decisions affecting more than one component.

## invariants.md

```markdown
# Project Name — System Invariants

> Cross-cutting properties that must hold across the entire system

## Data Integrity

- Invariant about data consistency across components

## Communication

- Invariant about inter-component interaction

## Identity and Access

- Invariant about authentication/authorization across the system
```

Only promote an invariant here when it genuinely crosses component boundaries. Component-specific invariants belong in the component's own Invariants section.

## decisions.md

```markdown
# Project Name — Architecture Decisions

> System-level decisions that affect multiple components

- **YYYY-MM-DD: Decision title.** Reasoning explaining why and what alternatives were rejected.
```

Decisions scoped to one component belong in that component's Decisions section.

## Component spec sections

Every component spec has exactly nine sections. No additional sections. No YAML frontmatter.

### Heading and description

```markdown
# Component Name

> One-line purpose of this component
```

The `#` heading is the human-readable component name. The `>` blockquote is a single-line description. Not a paragraph, not multiple lines.

### Stories

```markdown
## Stories

### Story title

Narrative paragraph describing a user or system interaction with this component.
```

Stories explain why the component exists through concrete scenarios. Written as prose paragraphs, not bullet lists or acceptance criteria. Each story gets a `###` heading. Not test cases — no GIVEN/WHEN/THEN.

### Accepts

```markdown
## Accepts

- **name** (required, type hint) — description
- **name** (optional, type hint) — description
```

Every input the component receives: API parameters, message fields, configuration values, user actions. Use natural language type hints in parentheses: `(required, object)`, `(optional, one of: a, b, c)`. Not code types like `string` or `Dict[str, Any]`. Not internal state or private variables.

### Produces

```markdown
## Produces

- **name** (type hint) — description
```

Every output the component emits: return values, events, side effects, files written. Same format as Accepts but without `required/optional`. Not internal state transitions.

### Behaviors

```markdown
## Behaviors

### Behavior name

Prose description of how the component acts in this situation.
```

Named scenarios with `###` headings. Describe what happens, not how it is implemented. Cover happy path, error paths, and edge cases. No implementation details like function calls or data structure choices.

### Boundaries

```markdown
## Boundaries

- Hard limit or rejection condition
```

Bullet list of hard limits, rejection conditions, timeouts. Not aspirations or soft preferences.

### Invariants

```markdown
## Invariants

- Falsifiable property that must always hold
```

Falsifiable statements that can be checked at any point in time. If you cannot describe how to verify it, it is too vague.

### Depends on

```markdown
## Depends on

- [Component](component-name.md) — what it gets from that component
```

Components this one requires. Relative Markdown links. Describe what it gets, not the mechanism.

### Exposes to

```markdown
## Exposes to

- [Component](component-name.md) — what it provides to that consumer
```

Same format as Depends on. What this component provides to each consumer.

### Decisions

```markdown
## Decisions

- **YYYY-MM-DD: Decision title.** Reasoning explaining why and what alternatives were rejected.
```

Implementation choices scoped to this component. Bold date prefix. Write the reasoning, not just the conclusion. Never remove entries — they are historical record. System-wide decisions belong in `decisions.md`.

## Formatting conventions

- Heading levels: `#` for component/project name, `##` for sections, `###` for subsections within Stories, Behaviors, and other sections that need them.
- Top-level description: `>` blockquote syntax. Always one line.
- Cross-references: relative Markdown links. `[Name](filename.md)` for same directory, `[Name](components/filename.md)` from index.
- Dates in decisions: `**YYYY-MM-DD: Title.**` — bold, colon after date, period after title, then reasoning.
- Type hints: natural language in parentheses. `(required, text)`, `(optional, list of URLs)`. Not programming language types.
- Placeholder for incomplete sections: `<!-- needs review -->`.
- No YAML frontmatter in spec files.
- No emoji.
