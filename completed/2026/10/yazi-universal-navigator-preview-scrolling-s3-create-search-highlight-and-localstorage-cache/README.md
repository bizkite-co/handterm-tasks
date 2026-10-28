---
created_at: 2026-10-28T09:05:48.322414-07:00
subtask_of: yazi-like-file-browse-br
---

# Yazi universal navigator: preview scrolling, S3 create, search highlight, and localStorage cache

# Yazi-Inspired Universal Content Navigator (`y` / `br`)

Universal text-driven content navigator for GitHub and S3 in Handterm.

## Requirements & Scope

1. **Invocation**:
   - `y` (or `y ENTER`) and `br` open the navigator.
   - Root mounts: `[GitHub, S3]`.
   - Default view: `GitHub` selected in the left/resource view, top 10 most recent repositories listed in the right column.

2. **Caching**:
   - Cache repository and file listings in `localStorage` for instant cold-start navigation.
   - Revalidate and refresh data asynchronously in the background.

3. **Layout & Hierarchy**:
   - Clean top breadcrumb showing current path (with `/` returning to the root resource list `[GitHub, S3]`).
   - Two/three-column layout with responsive collapse on narrow screens.

4. **Document Preview & Scrolling**:
   - Highlighting a file displays its content preview in the right column.
   - Use `Shift+J` and `Shift+K` to scroll up and down within the previewed subject document.

5. **Search & Highlighting**:
   - Yazi-like search filter (`/`) for directory/resource listings.
   - In-document search within the active preview pane with match highlighting.

6. **Resource Creation (S3)**:
   - Yazi syntax (`a` key) to create new S3 resources (trailing `/` creates a directory/prefix, otherwise creates a file).

7. **Vim Editor Handoff**:
   - `ENTER` on any file (S3 or GitHub blob) opens the document in the Handterm Monaco/Vim editor (`ActivityType.EDIT`).


## Completion Criteria

1. Command `y` and `br` open the navigator with GitHub and S3 resources.
2. Top 10 recent GitHub repos load from localStorage instantly and refresh in the background.
3. Highlighting a file renders a preview; `Shift+J` and `Shift+K` scroll the preview pane.
4. `a` key allows creating new files/folders in S3.
5. In-document search and highlighting are functional.
6. `ENTER` on S3 or GitHub files opens the document in Monaco Vim editor.
7. Unit tests and e2e integration verified.

## Solution

Implemented full Yazi-inspired universal navigator features:
1. Aliased 'y' command to 'br' in BrCommand.
2. Enabled 'q' and 'Escape' to immediately exit navigator and return to normal terminal prompt.
3. Added '/' shortcut to jump directly back to root mounts ([GitHub, S3]).
4. Added terminal braille spinner in breadcrumb bar and column sync status.
5. Implemented responsive child-column file preview for S3 and GitHub files with cached localStorage SWR.
6. Added Shift+J and Shift+K smooth scrolling for the preview pane.
7. Bound 'Enter' to fetch content, save to localStorage editContent, and open directly in the Monaco/Vim editor.
8. Implemented 'a' key prompt in S3 to create files or folders via AWS S3 putFile.
9. Implemented 's'/'f' search prompt with real-time filtering and <mark> tag match highlighting.
10. Added unit tests for all navigation, preview, scroll, open, create, search, and exit capabilities. Verified full test suite (42 test files, 202 tests) and clean production build.

---
**Completed in commit:** `no_code_commit`
