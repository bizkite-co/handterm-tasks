---
created_at: 2026-08-25T11:59:13.281330-07:00
---

# Schema-validate Lambda and awsApiClient responses with @effect/schema

Adopt @effect/schema on the Lambda/awsApiClient boundary to catch silent data-shape bugs (the class that caused the 'blank document' symptom earlier this session).

Scope:
- handterm/src/utils/awsApiClient.ts: getFile, putFile, listFiles, deleteFile — define response Schemas and validate with Schema.decodeUnknown before returning.
- handterm-cdk/lambda/userStorage/*.ts: validate incoming request payloads and outgoing response shapes.
- Share the Schemas via packages/types so client and Lambda agree.

Why: the API client currently returns loosely-typed blobs; a missing `content` field silently yields a blank editor. Schema validation makes that a loud, typed failure.

Non-goal: do not rewrite the activity mediator or state machine (see separate decision).

## Completion Criteria

getFile, putFile, listFiles, and deleteFile in handterm/src/utils/awsApiClient.ts validate their responses (and the matching handterm-cdk Lambda handlers validate their payloads) against @effect/schema Schemas. Malformed shapes surface as a typed error instead of returning undefined/any. Unit tests cover a happy-path and a malformed-shape rejection for each of the 4 functions.
