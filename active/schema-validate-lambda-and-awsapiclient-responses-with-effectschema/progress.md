# Progress

## Client side — DONE (committed-pending)
- Added `@effect/schema` dep to `packages/types/package.json`.
- Created shared schemas in `packages/types/src/api.ts` (exported via `index.ts`): `FileContentResponseSchema`, `MessageResponseSchema`, `ListFilesResponseSchema`, plus request schemas `PutFileRequestSchema` / `DeleteFileRequestSchema` / `ListFilesRequestSchema` / `GetFileRequestSchema`.
- Rebuilt `packages/types` (dist now emits `api.js`).
- `handterm/src/utils/awsApiClient.ts`: `makeAuthenticatedRequest` accepts an optional `responseSchema`; the 4 wrappers (`getFile`/`putFile`/`listFiles`/`deleteFile`) pass their schema. Malformed shapes return a typed `{ status: 502, error: 'Invalid response shape: ...' }` instead of undefined data.
- Tests: `src/__tests__/utils/awsApiClient.schema.test.ts` — happy + malformed for all 4 functions (9 tests, passing). Existing awsApiClient test still passes.

This catches the "blank document" bug class (a missing `content` field now fails loudly instead of yielding an empty editor).

## Lambda side — BLOCKED on bundling
`handterm-cdk` Lambdas use `Code.fromAsset('dist/lambda/userStorage')` with NO bundler and rely on the Node 20 runtime for `@aws-sdk/client-s3`. `@effect/schema` is not in the Lambda runtime, so validating Lambda payloads requires either:
  (a) switching the 4 userStorage Lambdas to `NodejsFunction` (esbuild) so `@effect/schema` + shared schemas bundle in, or
  (b) vendoring `@effect/schema` (and transitive deps) into the asset.

Both require a CDK redeploy. Split into subtask `validate-userstorage-lambda-request-payloads-with-effectschema-requires-bundling` (subtask of this task). Same bundling prerequisite also gates the Lambda portion of the error-handling task.

## API note
`@effect/schema` 0.75.5 API: `Schema.Struct({...})` (capital S), `Schema.String` / `Schema.Number` / `Schema.Array` / `Schema.optional`, `Schema.decodeUnknownEither(schema)(input)` → `Either` with `_tag` `Right`/`Left`, `.right`, `.left.message`.
