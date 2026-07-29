---
created_at: 2026-07-28T20:47:54.441602-07:00
---

# Fix useActivityMediator tutorial skip test timing

The test at `src/__tests__/hooks/useActivityMediator.test.ts:45` expects `isInNormal` to become `true` but gets `false`. This is likely a timing/async issue — the test sets localStorage and then immediately waits for `isInNormal`, but the hook may process the value asynchronously. Investigate the test and the hook implementation to determine if there's a missing `waitFor` or the initial state isn't being read from localStorage before the component renders.

## Completion Criteria

The 'should skip tutorial when completed-tutorials exists in localStorage' test passes reliably
