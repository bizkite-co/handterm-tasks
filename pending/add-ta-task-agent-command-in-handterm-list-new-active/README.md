---
created_at: 2026-10-25T16:20:10.129807-07:00
---

# Add 'ta' task-agent command in Handterm (list, new, active)

### Background
Handterm's task store is tracked in the git repository `bizkite-co/handterm-tasks` (as configured in task-agent store registry for `bizkite-co/handterm`). Handterm already has authenticated GitHub API integration (`gh link`, `getRepoTree`, `saveRepoFile`), allowing it to interact directly with GitHub repositories.

### Objective
Implement a `ta` command in Handterm mirroring task-agent CLI conventions:
- `ta list`: Fetch repository tree of `bizkite-co/handterm-tasks`, parse task files across statuses (`active/`, `pending/`), and display formatted list of tasks.
- `ta new <title> [-c <criteria>] [-b <body>]`: Slugify title, format standard task markdown with ISO timestamp frontmatter and completion criteria, and commit `pending/<slug>/README.md` to `bizkite-co/handterm-tasks` via `saveRepoFile`.
- `ta active <slug>`: Move the task from `pending/` to `active/` in the remote store and commit.

### Requirements
- Target repo configurable or defaulting to `bizkite-co/handterm-tasks` (or configured store moniker).
- Require GitHub authentication; prompt with `gh link` instructions if unauthenticated.
- Register `ta` (and alias `task`) in `src/commands/commandRegistry.ts`.
- Full unit test coverage in `src/commands/__tests__/TaCommand.test.ts`.

## Completion Criteria

1. `ta` command registered in Handterm command registry with subcommands `list`, `new`, `active` (and `task` alias).
2. `ta list` displays active and pending tasks from `bizkite-co/handterm-tasks` with slug and title.
3. `ta new` creates and commits valid task Markdown file with frontmatter and completion criteria to `pending/<slug>/README.md`.
4. `ta active <slug>` activates the specified pending task.
5. Displays clear error if GitHub account is not linked (`gh link`).
6. Unit test suite passes verifying command parsing, GitHub API interaction, and output formatting.
