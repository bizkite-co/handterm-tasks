---
created_at: 2026-10-13T10:44:22.320751-07:00
subtask_of: yazi-like-file-browse-br
---

# MVP: `br` miller-column browse over GitHub and S3

First slice only. No Lambda delimiter change, no GitHub path drill past repo list, no preview pane, do not rewrite `ls` or `gh tree` yet.

Add ActivityType.BROWSE. FileSystem list() adapters wrapping listFiles and listRecentRepos. BrowseView React pane in HandTermWrapper (hide prompt like EDIT). Focus capture so hjkl do not hit the terminal.

## Completion Criteria

`br` switches to a BROWSE activity. Root lists GitHub and S3. hjkl + Enter + Escape work. Three columns on desktop, one column at max-width 600px. S3 lists Cognito user files (group keys on `/` for a first folder level). GitHub lists recent repos after link, otherwise an empty state pointing at `gh link`. S3 401 empty state points at login. Enter on an S3 file opens existing EDIT. Unit tests cover grouping, keyboard actions, and the br command. Help lists `br`.
