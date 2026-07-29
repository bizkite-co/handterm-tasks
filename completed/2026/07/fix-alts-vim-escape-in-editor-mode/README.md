---
created_at: 2026-07-28T23:06:57.676843-07:00
---

# Fix alt+s vim escape in editor mode

Add alt+s keydown handler to editor MonacoCore (same as terminal's useMonacoTerminal handler). Track vim mode via vim-mode-change event. Make alt+s idempotent - if already in normal mode, just consume the event without sending another &lt;Esc&gt;.

## Completion Criteria

alt+s exits insert mode in both terminal and editor MonacoCore instances, is idempotent when pressed multiple times in normal mode

## Solution

Added `currentVimModeRef` and `editorKeydownRef` to MonacoCore. The vim-mode-change event now tracks mode internally. In editor mode, an editor.onKeyDown handler intercepts Alt+S: when in insert mode it sends `<Esc>` via monacoVim.VimMode.Vim.handleKey; when already in normal mode it silently consumes the event — fully idempotent.

---
**Completed in commit:** `8edb6b7`
