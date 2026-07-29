---
created_at: 2026-07-28T23:06:57.676843-07:00
---

# Fix alt+s vim escape in editor mode

Add alt+s keydown handler to editor MonacoCore (same as terminal's useMonacoTerminal handler). Track vim mode via vim-mode-change event. Make alt+s idempotent - if already in normal mode, just consume the event without sending another &lt;Esc&gt;.

## Completion Criteria

alt+s exits insert mode in both terminal and editor MonacoCore instances, is idempotent when pressed multiple times in normal mode
