# CLAUDE.md: ReschedulingEngine (Team Aurora)

## Context
- Python service for customer rescheduling of jobs.
- Flow: POST /reschedule -> eligibility check -> SchedulerV2 gRPC -> Kafka `job.rescheduled` -> NotificationService.
- Storage: PostgreSQL (`reschedule_events` audit log). Messaging: Kafka. Flags: `enable_v2_reschedule`.
- Code: `services/rescheduling/` (api, handler, scheduler_client). Tests: `tests/`.
- Read the relevant design doc in `docs/design/` before changing behaviour.

## Commands
- Tests: `pytest tests/ -v`
- Lint: `ruff check services/ tests/` (and `ruff format --check .`)
- Run tests and lint before saying a task is done, and show the result.

## How to work
- For a change over ~30 lines or touching more than 2 files, write a short plan first and wait for my OK.
- Keep changes small. Do not refactor unrelated code.
- If unsure, say so and ask one specific question. Never guess business rules.
- Challenge my approach when you see a risk: say what you would do instead and why.

## Engineering rules for this service
- Every write path is idempotent (idempotency key). Retries must not create duplicate events.
- Never skip the eligibility check.
- Handle errors explicitly: timeouts, retryable vs non-retryable, partial failure. No bare TODO for error handling.
- No blocking calls inside async handlers.
- Kafka events have an event ID and are keyed by job ID.
- New behaviour ships behind a feature flag with a rollback path.
- Tests for every behaviour change: happy path plus failure paths (timeout, duplicate, partial failure).
- Add logs and metrics for new code paths. No PII in logs.

## Boundaries
- Do freely: read files, edit code, run tests and lint, create branches.
- Ask first: add dependencies, change DB schema or migrations, delete files, git push, change flags or configs.
- Never: touch production, run deploy scripts, print or commit secrets, read .env files, run destructive commands (rm -rf, DROP, force-push).

## Security
- Secrets come from the environment or the secret manager, never from code, docs, or this file.
- If you find a secret in the repo, stop and tell me. It must be rotated.

## Definition of done
- Tests and lint pass. The PR says what and why, links the ticket and design, and lists risks and rollback.

## Maintenance
- This file is reviewed like code. When the AI repeats a mistake, add or sharpen one rule. Owner: Team Aurora EM.
