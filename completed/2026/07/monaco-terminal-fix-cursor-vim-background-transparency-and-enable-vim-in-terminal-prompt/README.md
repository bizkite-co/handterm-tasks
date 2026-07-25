---
created_at: 2026-07-24T18:50:54.046264-07:00
---

# Monaco Terminal — Fix cursor, vim background transparency, and enable vim in terminal prompt

## Incident Summary

The Monaco-based terminal prompt had three bugs when running locally after several months of inactivity:

1. **Cursor stuck at first position** — typing appended text but cursor remained at position (1,1)
2. **NVim background mismatch** — monaco-vim background didn't match the terminal background
3. **Vim in terminal prompt** — vim was not enabled for terminal mode; user wanted it active with insert mode start

## Root Causes

### Issue 1: Cursor position reset on `setValue`
`useMonacoTerminal.ts` used `model.setValue(currentContent + data)` to append typed characters. Monaco's `setValue()` resets the cursor to position (1,1), causing the visual cursor to jump back to the start despite text being appended correctly.

### Issue 2: Semi-transparent span background
`terminal.css:458` had `div.view-line > span { background: #0007; }` — a semi-transparent black box behind each text span, causing a visible background mismatch against the transparent terminal.

### Issue 3: Vim only initialized for editor mode
`MonacoCore.tsx` Effect 3 ran `initVimMode` only when `mode === 'editor'`. `MonacoTerminal` passed `mode="terminal"`, so vim was never active in the prompt. Additionally, the `onKeyDown` handler in `useMonacoTerminal` intercepted ALL single-character keys (via `preventDefault`), preventing vim from ever seeing commands like `i`/`a`/`o` in normal mode — even if vim were active.

## Changes

| File | Change |
|---|---|
| `src/hooks/useCommand.ts:4` | Added missing `useMemo` to React import (pre-existing crash) |
| `src/hooks/useMonacoTerminal.ts` | Added `setValueAndFocusEnd` helper — calls `setValue()` then `setPosition()` to end + `revealLine()` — used at every `setValue` call site |
| `src/hooks/useMonacoTerminal.ts` | Key handler now checks `currentModeRef?.current === 'normal'` and returns early, letting vim handle all keys in normal mode |
| `src/hooks/useMonacoTerminal.ts` | Accepts optional `currentModeRef` param for vim mode tracking |
| `src/components/MonacoCore.tsx` | Vim init now runs for `mode === 'terminal'` too; calls `Vim.handleKey(vimInstance, 'i')` to start in insert mode; exposes `onVimModeChange` callback |
| `src/components/MonacoCore.tsx` | Status bar div always rendered (was editor-only — `initVimMode` needs a DOM node) |
| `src/components/MonacoTerminal.tsx` | Holds `currentModeRef`, passes `onVimModeChange` to MonacoCore and `currentModeRef` to `useMonacoTerminal` |
| `src/assets/terminal.css:458` | Changed `div.view-line > span` background from `#0007` to `#0000` (transparent) |
| `src/types/monaco-vim.d.ts` | Extended declarations to expose `VimMode`, `VimModeInstance`, `VimApi` (also fixed 2 pre-existing TS2339 errors) |

## Completion Criteria

All three reported issues resolved: cursor moves to end of typing, transparent vim background matching terminal, vim enabled in terminal prompt with insert mode start and normal-mode re-entry working

## Solution

Fixed three bugs in the Monaco-based terminal prompt:

1. **Cursor position** — Added `setValueAndFocusEnd()` helper that moves cursor to end after every `setValue()` call. All 7 call sites updated in useMonacoTerminal.ts.
2. **Background transparency** — Changed `div.view-line > span { background: #0007 }` to `#0000` in terminal.css.
3. **Vim in terminal prompt** — Enabled `initVimMode` for terminal mode in MonacoCore, starts in insert mode via `Vim.handleKey(instance, 'i')`, and made the key handler mode-aware: in normal mode all keys defer to vim (fixing re-entry via i/a/o), in insert mode existing terminal handling runs.
4. **Bonus crash fix** — Added missing `useMemo` import in useCommand.ts.

---
**Completed in commit:** `a873f75`
