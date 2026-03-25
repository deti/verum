# Task Router

> Receives tasks, assigns priority, and dispatches them to the appropriate worker pool

## Stories

### Submitting a background job

A web service finishes handling a user request and needs to send a confirmation email. It submits a task to the router with a payload describing the email and a priority of "normal". The router validates the payload, assigns a task ID, and places it in the normal-priority queue. The caller gets back the task ID immediately and can poll for status later.

### Urgent task preemption

An ops engineer triggers a database backup through the admin API. The task is submitted with "critical" priority. The router places it at the front of the critical queue, which the worker pool drains before touching normal or low-priority work.

## Accepts

- **payload** (required, object) — the task body containing the work to perform. Must include a **type** field identifying the task handler.
- **priority** (optional, one of: critical, normal, low) — dispatch priority. Defaults to "normal" if omitted.
- **idempotency_key** (optional, text) — caller-provided key for deduplication. If a task with the same key was submitted in the last 24 hours, the router returns the existing task ID instead of creating a new one.
- **callback_url** (optional, URL) — webhook to notify when the task completes or permanently fails.

## Produces

- **task_id** (text) — unique identifier assigned to the accepted task. Returned synchronously on submission.
- **task.queued event** (event) — emitted when a task is placed in a priority queue. Includes task ID, priority, and queue depth at time of insertion.
- **task.rejected event** (event) — emitted when a task fails validation. Includes the rejection reason.
- **callback POST** (HTTP request) — sent to the callback URL when the task reaches a terminal state (completed or failed after exhausting retries).

## Behaviors

### Payload validation

The router checks that the payload contains a **type** field and that the type matches a registered task handler. If validation fails, the task is rejected immediately with a reason describing what was wrong. No task ID is assigned for rejected tasks.

### Priority queue dispatch

Accepted tasks are placed in one of three queues based on priority. Critical tasks go to the front of the critical queue. Normal and low tasks are appended to their respective queues in arrival order. The worker pool always drains the critical queue first.

### Idempotency deduplication

When an idempotency key is provided, the router checks a 24-hour rolling window of recent keys. If a match is found, the router returns the original task ID without creating a duplicate. The deduplication window is not configurable.

### Callback delivery

When a callback URL is provided and the task reaches a terminal state, the router sends a POST request with the task ID, final status, and result or error. Callback delivery is best-effort — one attempt, no retries. Failed callbacks are logged but do not affect task status.

## Boundaries

- Payload size must not exceed 256 KB. Larger payloads are rejected.
- The router accepts at most 1000 tasks per second per caller. Excess submissions receive a rate-limit rejection.
- Idempotency keys are stored for 24 hours, then evicted. Resubmitting after eviction creates a new task.
- The router does not inspect or transform payload contents beyond checking the type field. Payload validation is the handler's responsibility.
- Callback URLs must use HTTPS. HTTP URLs are rejected.

## Invariants

- Every accepted task has a unique task ID that is never reassigned, even after the task completes or fails.
- A task is in exactly one state at any time: queued, running, completed, or failed.
- The critical queue is always drained before the normal queue, and normal before low.

## Depends on

- [Worker Pool](worker-pool.md) — dispatches queued tasks to available workers for execution
- [Retry Manager](retry-manager.md) — hands off failed tasks for rescheduling with backoff

## Exposes to

- [Retry Manager](retry-manager.md) — accepts re-queued tasks from the retry manager as if they were new submissions, preserving the original task ID

## Decisions

- **2026-03-10: Three fixed priority levels.** Considered numeric priorities (1-100) but fixed levels are simpler to reason about and prevent priority inversion games. Three levels cover the actual use cases: ops-critical, normal work, and deferrable cleanup.
- **2026-03-18: Best-effort callbacks with no retries.** Callback delivery retries add a queue-within-a-queue. Callers who need reliable notification should poll the status endpoint instead. If callback reliability becomes a real problem, add a dedicated notification component rather than complicating the router.
- **2026-03-22: 24-hour idempotency window.** Shorter windows risk duplicate tasks from slow retries. Longer windows consume more memory. 24 hours covers the worst-case retry scenarios we've seen without unbounded storage growth.
