---
created_at: 2026-10-13T10:44:22.419457-07:00
blocked_by: mvp-br-miller-column-browse-over-github-and-s3
subtask_of: yazi-like-file-browse-br
---

# Nested S3 prefixes for miller columns

Either group remaining path segments client-side or add ListObjectsV2 Delimiter and prefix to the ListFiles Lambda. Prefer the smallest change that makes miller columns honest.

## Completion Criteria

S3 mount supports directory drill-in for keys with `/`. Listing a prefix shows only that level. Opening a nested file still uses getFile/EDIT. Tests cover multi-segment keys.
