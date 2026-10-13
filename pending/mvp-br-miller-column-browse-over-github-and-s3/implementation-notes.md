## MVP implementation (2026-10-13)

Shipped in the Handterm working tree (not completed in the queue until you try `br` in the app):

- `ActivityType.BROWSE` (`activity=browse`)
- `br` command (optional `br s3` / `br github` / `br gh`)
- `BrowseView` miller columns: parent | current | child; one column at `max-width: 600px`
- Keys: h parent, j/k move, l/Enter open, Escape back to NORMAL
- Root mounts GitHub and S3; S3 groups keys on `/`; GitHub lists recent repos then one grouped tree level
- Logged-out S3 and failed GitHub list show empty-state rows
- Enter on an S3 file loads `getFile` and opens EDIT

Tests: `browseFs.test.ts`, `BrCommand.test.ts`, `BrowseView.test.tsx` (19 tests with existing chars tests).

Not in this slice: Lambda delimiter, GitHub blob preview, rewriting `ls` / `gh tree`.
