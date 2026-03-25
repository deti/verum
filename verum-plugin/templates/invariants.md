# [Project Name] — System Invariants

> [Cross-cutting properties that must hold across the entire system]

<!-- This file collects invariants that span multiple components. Use it when a
     property cannot be owned by a single component — for example, "every API
     response includes a request ID" touches every service, not just one.

     Component-specific invariants belong in the component's own Invariants
     section. Only promote an invariant here when it genuinely crosses
     component boundaries.

     Write each invariant as a falsifiable statement. If you cannot describe how
     to check whether the invariant holds, it is too vague. -->

## Data Integrity

<!-- Invariants about data consistency, referential integrity, and storage
     guarantees that span components. -->

- [Invariant about data that crosses component boundaries]

## Communication

<!-- Invariants about how components interact — message ordering, delivery
     guarantees, protocol constraints. -->

- [Invariant about inter-component communication]

## Identity and Access

<!-- Invariants about authentication, authorization, and identity propagation
     across the system. -->

- [Invariant about identity or access control across components]
