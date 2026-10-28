# Subdirectory Navigation & Storage Architecture Plan

## 1. Issue: GitHub Subdirectory Browsing & 'child' Placeholder (RESOLVED)
- **Root Cause**: `src/utils/browseFs.ts` previously synthesized a fake `${item.path}/child` item for entries of type `tree`/`directory` via `topLevelTreeKeys()`. When `listGithubTree(repo)` was called without a `path` parameter, only root entries were returned from the API. When navigating into `/github/:owner/:repo/:subpath`, `browseFs` filtered the cached root list and found only the synthetic `child` item. Selecting `child` attempted to fetch non-existent file `${subpath}/child`.
- **Implemented Fix**:
  1. Updated `BrowseListDeps.listGithubTree` to accept `(repo: string, path?: string)`.
  2. In `BrowseView.tsx`, passed the active `nested` directory path into `getRepoTree(authRef.current, repo, path)`.
  3. Replaced `topLevelTreeKeys` synthetic hack with `parseGithubTreeEntries()`, which properly strips nested path prefixes, distinguishes directories from files, and skips any legacy `child` tokens.
  4. Added test cases in `src/utils/browseFs.test.ts` verifying GitHub subdirectory contents without synthetic items.

## 2. Storage Media & Cache Architecture (IMPLEMENTED)
- **Problem with `localStorage`**: Limited to 5 MB per origin, synchronous/blocking I/O. Parsing large JSON trees on navigation freezes the UI and quickly triggers `QuotaExceededError`.
- **Implemented Solution**:
  1. Created `src/utils/browseStorage.ts` featuring:
     - L1 In-Memory Cache: synchronous Map for instant zero-latency UI updates during component lifecycle.
     - L2 IndexedDB Cache (`HandTermBrowseCache`, v1): non-blocking asynchronous storage with object stores for `entries` and `previews`.
     - Automatic purge of legacy `browse_cache:*` and `browse_preview:*` items from `localStorage`.
  2. Wired `BrowseView.tsx` to read synchronously from L1 on render and fallback to L2 IndexedDB, updating storage asynchronously in the background.
  3. Added comprehensive test suite in `src/utils/__tests__/browseStorage.test.ts`.

## 3. Verification
- All 43 test files (216 tests) passed.
- Production build succeeded cleanly.
- Committed in commit `f3a3629`.
