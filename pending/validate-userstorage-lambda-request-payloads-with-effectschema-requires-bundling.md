---
created_at: 2026-08-25T15:59:32.678676-07:00
subtask_of: schema-validate-lambda-and-awsapiclient-responses-with-effectschema
---

# Validate userStorage Lambda request payloads with @effect/schema (requires bundling)

Blocked on a CDK bundling change + redeploy — do NOT start without user sign-off (deploy was just unblocked).\n\nCurrent state: handterm-cdk Lambdas use `Code.fromAsset('dist/lambda/userStorage')` with no bundler; @effect/schema is not in the Node 20 Lambda runtime.\n\nApproach (recommended): switch the 4 userStorage Lambdas (S3GetFile/S3PutFile/S3ListFiles/S3DeleteFile) to `lambda.NodejsFunction` (esbuild) so @effect/schema + the shared schemas from packages/types bundle into a single file. Add @effect/schema to handterm-cdk deps and @handterm/types (file:../handterm/packages/types). Then import PutFileRequestSchema/DeleteFileRequestSchema/ListFilesRequestSchema/GetFileRequestSchema and Schema.decodeUnknownEither the parsed body/query; return 400 on Left.\n\nThis same bundling change also unblocks the Lambda portion of the error-handling task (standardize-error-handling...).\n\nShared schemas already exist in packages/types/src/api.ts (see parent task progress doc).

## Completion Criteria

The 4 userStorage Lambda handlers (getFile/putFile/listFiles/deleteFile) validate their incoming request payloads against the shared @effect/schema schemas from packages/types, returning 400 on malformed bodies. @effect/schema is available at Lambda runtime via esbuild bundling (NodejsFunction) or vendoring. CDK deploy succeeds and a malformed payload gets a 400 while a valid one still works.
