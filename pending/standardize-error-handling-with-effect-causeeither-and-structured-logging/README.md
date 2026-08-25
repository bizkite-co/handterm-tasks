---
created_at: 2026-08-25T11:59:13.434354-07:00
---

# Standardize error handling with Effect Cause/Either and structured logging

Replace ad-hoc try/catch + console.error / logger.error patterns with Effect's typed error channel (Cause/Either) and a consistent structured logger, so failures stop being silently swallowed.

Scope:
- handterm/src/components/MonacoCore.tsx defineVimCommands: :w/:q/:q!/:wq currently catch and log but can swallow navigation/localStorage failures.
- handterm/src/utils/awsApiClient.ts: network/auth errors should return typed errors.
- handterm-cdk/lambda/userStorage/*.ts: surface Lambda failures as typed errors.

Why: these paths currently swallow errors (e.g., putFile upload failure is logged but the editor gets no signal). An Effect error channel makes failures first-class and testable.

Non-goal: no state-machine migration; this is additive to the existing createLogger.

## Completion Criteria

vim command handlers in MonacoCore.tsx (:w/:q/:q!/:wq) and the awsApiClient + Lambda call sites replace try/catch+console.error with Effect's error channel (Effect.fail / Either.Left) so failures are explicit and reportable. No bare console.error remains in these paths. Structured logger is used consistently. Tests assert that error paths propagate a typed error rather than swallowing it.
