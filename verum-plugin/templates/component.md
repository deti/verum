# [Component Name]

> [One-line purpose of this component]

<!-- A component is a cohesive unit of behavior — a service, module, subsystem,
     or bounded context. The sections below are the complete schema. Do not add
     sections beyond these. Do not add YAML frontmatter. -->

## Stories

<!-- Stories are short narratives describing how a user (or another system)
     interacts with this component. They are not acceptance criteria or test cases.
     Write them as plain paragraphs, not bullet lists. Each story should make a
     reader understand one reason this component exists. -->

### [Story title]

[Narrative paragraph describing a user interaction with this component]

## Accepts

<!-- List every input this component receives: API parameters, message fields,
     configuration values, user actions. Use natural language with parenthetical
     type hints — not code-block type definitions.
     Format: - **name** (required/optional, type hint) — description -->

- **[input name]** ([required/optional], [type hint]) — [description]

## Produces

<!-- List every output this component emits: return values, events published,
     side effects, files written. Same format as Accepts but without
     required/optional since outputs are always produced when their conditions
     are met. -->

- **[output name]** ([type hint]) — [description]

## Behaviors

<!-- Describe how the component acts in specific situations. Each behavior is a
     named scenario with a prose description. Focus on what happens, not how it
     is implemented. Cover the happy path, error paths, and edge cases. -->

### [Behavior name]

[Description of how the component behaves in this situation]

## Boundaries

<!-- Hard limits, rejection conditions, and edge cases. These are the walls of
     the component — things it refuses to do, size limits, rate limits, timeout
     values. If a behavior section says "it handles X", a boundary says "it
     rejects Y". -->

- [Hard limit, edge case, or rejection condition]

## Invariants

<!-- Properties that must always hold for this component, regardless of input or
     state. Write them as falsifiable statements that could be checked at any
     point in time. "The cache never contains expired entries" is an invariant.
     "The cache should be fast" is not. -->

- [Property that must always hold for this component]

## Depends on

<!-- Components this component reads from, calls, or otherwise requires.
     Link to the spec file. Describe what it gets, not the mechanism. -->

- [Component](filename.md) — [what it gets from that component]

## Exposes to

<!-- Components that depend on this component. Link to the spec file.
     Describe what this component provides to each consumer. -->

- [Component](filename.md) — [what it provides]

## Decisions

<!-- Decisions scoped to this component — implementation choices, trade-offs,
     rejected alternatives. System-wide decisions belong in the project index.
     Use bold date prefix. Write the reasoning, not just the conclusion. -->

- **[YYYY-MM-DD]: [Decision].** [Reasoning.]
