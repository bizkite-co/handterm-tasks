---
created_at: 2026-10-13T10:44:04.395675-07:00
---

# Yazi-like file browse (`br`)

Unified file navigator for Handterm, opened with `br`.

Root objects: `GitHub` (linked account, Cognito GitHub token) and `S3` (Cognito-partitioned `user_data/{userId}/` via existing listFiles/getFile/putFile).

Layout: Yazi-style miller columns (parent | current | child). Collapse to one column at the existing 600px terminal breakpoint. Keys: h parent, j down, k up, l enter dir, Enter open file, Escape return to NORMAL.

Reuse: listFiles, getFile, listRecentRepos, getRepoTree, ActivityType slot (add BROWSE), existing EDIT for opening files. Do not embed Yazi/WASM.

Out of scope for v1 polish subtask: image preview, fuzzy filter, copy between mounts.

Work the MVP subtask first.

## Completion Criteria

Command `br` opens a miller-column navigator whose root mounts are GitHub and S3. hjkl navigation works. Desktop shows three columns and a narrow viewport shows one. Enter opens a file in the existing EDIT activity. Auth empty states send the user to login or `gh link`. Remaining nested-S3, GitHub drill-in, preview, and ls/gh-tree wiring live as subtasks; this parent stays open until those v1 subtasks complete or are explicitly dropped.
