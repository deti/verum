# Vibe

> Background task processing service with priority scheduling and retry logic

## Components

- [Task Router](components/task-router.md) — receives tasks, assigns priority, dispatches to worker pools
- [Worker Pool](components/worker-pool.md) — manages a fixed set of workers that execute tasks concurrently
- [Retry Manager](components/retry-manager.md) — tracks failed tasks and reschedules with backoff

## System Invariants

- Every task that enters the system either completes successfully, exhausts its retry limit, or is explicitly cancelled. No task is silently dropped.
- Task IDs are unique across the lifetime of the service. Restarting the service does not reuse IDs.

## Architecture Decisions

- **2026-03-10: Single-process architecture.** Vibe runs as one process with in-memory queues. Distributed coordination adds complexity we don't need at current scale. Revisit if task volume exceeds what one machine handles.
- **2026-03-18: Priority is caller-assigned, not inferred.** The router accepts priority from the caller rather than inferring it from task type. Inference would require maintaining a classification model and adds a failure mode. Callers know their own urgency.
