# Verum Spec Format Reference

Canonical component spec format. No additional sections beyond these.

## Structure

- `#` component name, then `>` blockquote description (one line)
- `##` canonical sections, `###` subsections within Stories and Behaviors

## Sections

### Stories
Narrative paragraphs (not acceptance criteria) with `###` headings. Describes why this component exists. Not implementation details.

### Accepts
Every input: `- **name** (required/optional, type hint) — description`. Natural language type hints, not code types. API parameters, message fields, config values, user actions. Not internal state.

### Produces
Every output: `- **name** (type hint) — description`. No required/optional marker. Return values, events, side effects, files written. Not internal state transitions.

### Behaviors
Named scenarios with `###` headings and prose. What happens, not how. Cover happy path, errors, edge cases. Not implementation algorithms.

### Boundaries
Bullet list of hard limits, rejection conditions, edge cases. Not aspirational goals or soft preferences.

### Invariants
Falsifiable statements that must always hold. "The cache never contains expired entries" yes. "The cache should be fast" no.

### Depends on
`- [Component](component-name.md) — what it gets`. Relative Markdown links. Describe what, not mechanism.

### Exposes to
Same format as Depends on. What this component provides to each consumer.

### Decisions
`- **YYYY-MM-DD: Decision title.** Reasoning.` Bold date prefix. Write reasoning, not just conclusion. Never remove entries — historical record.

## Cross-references and system files

Relative Markdown links: `[Name](component-name.md)`. Bare filenames for same directory.

System-level files: `index.md` (component list, system invariants, architecture decisions), `invariants.md` (cross-cutting invariants), `decisions.md` (system-wide decisions).
