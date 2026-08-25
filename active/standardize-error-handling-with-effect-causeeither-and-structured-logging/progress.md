# Progress

## Client side — DONE
- Added `effect` ^3.17.10 as a direct dep of handterm (already present transitively via @effect/schema 0.75.5; era-correct Either source).
- `MonacoCore.tsx`:
  - New exported `SaveError extends Data.TaggedError('SaveError')<{ message: string }>` (Effect typed error).
  - `defineVimCommands` now takes an optional `onSaveResult?: (result: Either<void, SaveError>) => void`.
  - `saveToAWS` returns `Promise<Either<void, SaveError>>` and never swallows: auth-missing → Left, non-200 → Left, thrown → Left, 200 → Right.
  - `:w` and `:wq` call `saveToAWS(content).then(handleSaveResult)`; `handleSaveResult` routes to `onSaveResult`, or logs the Left if none wired (still not swallowed).
  - New prop `onSaveResult` on `MonacoCoreProps`; wired through to `defineVimCommands` and added to Effect 3 deps.
- Tests: `src/components/MonacoCore.save.spec.tsx` (5 tests, passing) — covers: putFile rejects → typed SaveError Left; 200 → Right; non-200 → Left; missing auth → Left (putFile not called); no onSaveResult → no throw/settle.
- `awsApiClient.ts` schema-validation path already uses @effect/schema's `Either` (decodeUnknownEither) and returns a typed 502 on malformed shapes (done under the schema task). Network/auth errors are already surfaced as `{ status, error }` (not swallowed) and use `logger.error` (no bare `console.error`).

This fixes the actual swallowing bug called out in the task body: "putFile upload failure is logged but the editor gets no signal" — failures now propagate as a typed `Either` via `onSaveResult`.

## Lambda side — BLOCKED on bundling (shared with schema task)
Same prerequisite as the schema task's Lambda portion: handterm-cdk Lambdas use `Code.fromAsset('dist/lambda/userStorage')` with no bundler and rely on the Node 20 runtime for `@aws-sdk/client-s3`; `@effect/schema`/`effect` are not in the Lambda runtime. Needs `NodejsFunction` (esbuild) switch or vendoring + a CDK redeploy. Tracked in subtask `validate-userstorage-lambda-request-payloads-with-effectschema-requires-bundling`.

## Notes
- `effect` and `@effect/schema` were added to handterm `package.json` deps; both are already in node_modules, so no install was needed to run tests, but `npm install` should be run to refresh the lockfile.
- `@effect/schema` 0.75.5 uses `Schema.Struct` (capital S); `effect` 3.17.10 provides `Either`/`Data.TaggedError`.
