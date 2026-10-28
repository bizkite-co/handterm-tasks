# Subdirectory Navigation & Storage Architecture Plan

## 1. Issue: GitHub Subdirectory Browsing & 'child' Placeholder
- **Root Cause**: `src/utils/browseFs.ts` synthesized a fake `${item.path}/child` item for entries of type `tree`/`directory` via `topLevelTreeKeys()`. When `listGithubTree(repo)` was called without a `path` parameter, only root entries were returned from the API. When navigating into `/github/:owner/:repo/:subpath`, `browseFs` filtered the cached root list and found only the synthetic `child` item. Selecting `child` attempted to fetch non-existent file `${subpath}/child`.
- **Fix**:
  1. Update `BrowseListDeps.listGithubTree` to accept `(repo: string, path?: string)`.
  2. In `BrowseView.tsx`, pass the current `path` (or directory rest path) into `getRepoTree(auth, repo, path)`.
  3. In `browseFs.ts`, remove the synthetic `/child` hack in `topLevelTreeKeys` and properly process directory items and blobs returned for the requested path.

## 2. Storage Media & Cache Architecture
- **localStorage Limitation**: Limited to 5 MB per origin, synchronous/blocking I/O. Parsing large JSON trees on navigation causes UI freezing and will easily hit QuotaExceededError for real repositories.
- **IndexedDB Solution**:
  - Implement an asynchronous IndexedDB cache (`browseCache`) with object stores:
    - `repo_trees`: key `"{repo}:{path}"`, data: `{ items, timestamp }`
    - `file_previews`: key `"{repo}:{path}"` or `"s3:{key}"`, data: `{ content, timestamp }`
    - `s3_listings`: key `"s3:root"`, data: `{ files, timestamp }`
  - Render cached data instantly, background-revalidate against API, and update storage asynchronously without blocking UI interactions.
