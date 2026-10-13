---
created_at: 2026-10-13T10:44:22.531694-07:00
blocked_by: mvp-br-miller-column-browse-over-github-and-s3
subtask_of: yazi-like-file-browse-br
---

# GitHub path drill-in and text preview pane

MVP stops at recent repos. This slice is real tree navigation plus preview.

## Completion Criteria

From a GitHub repo, l/Enter walks tree paths via getRepoTree. Selecting a blob shows a text preview (getRepoContent) in the third column. Enter on a blob opens EDIT with that content. Unlinked GitHub still shows the link empty state.
