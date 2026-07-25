# Incident Report: Monaco Terminal Prompt Bugs

**Date:** 2026-07-24
**Branch:** `monaco-terminal`
**Severity:** Blocking (terminal prompt unusable)

---

## Reported Errors

```
client:756
 WebSocket connection to 'ws://localhost:5173/?token=n9LW1WwpuZkh' failed:
useMonacoTerminal.ts:24
 Uncaught SyntaxError: The requested module '/src/hooks/useWPMCaculator.ts'
 does not provide an export named 'useWPMCaculator' (at useMonacoTerminal.ts:24:10)

useCommand.ts:212
 Uncaught ReferenceError: useMemo is not defined
    at useCommand (useCommand.ts:212:5)
    at CommandProvider (CommandProvider.tsx:28:7)
```

After resolving the import errors, three functional issues remained:

1. Cursor stays at first position when typing in the terminal prompt
2. NVim background doesn't match terminal background
3. Vim not active in terminal prompt (user wanted Monaco/NVIM in prompt area)

---

## Investigation & Fixes

### Fix 0: Import crashes (pre-existing)

- `useMonacoTerminal.ts:24` imported `useWPMCaculator` (missing `l`) but file exports `useWPMCalculator` (with `l`). Fixed typo.
- `useCommand.ts:212` used `useMemo` but only imported `{ useContext, useState, useCallback, useEffect }` from React. Added `useMemo`.

### Fix 1: Cursor stuck at position (1,1)

**Root cause:** Every `m.setValue(currentContent + data)` call in `useMonacoTerminal.ts` resets Monaco's cursor to position (1,1). Text was appended correctly but the cursor visually jumped back to the start.

**Fix:** Added `setValueAndFocusEnd(value)` helper that calls `m.setValue(value)` then `editor.setPosition({ lineNumber: m.getLineCount(), column: m.getLineMaxColumn(lastLine) })` and `editor.revealLine(lastLine)`. Replaced every `m.setValue(...)` call with this helper.

**Call sites updated:**
- `writeOutputInternal` — output writes
- `resetPrompt` — prompt reset
- `navigateHistory` — history navigation
- `handleEnterKey` — newline + prompt reset
- `handleBackspace` — character deletion
- `handleData` — Ctrl+C output, regular character input
- `clear` — terminal clear

### Fix 2: NVim background mismatch

**Root cause:** `terminal.css:458` had `div.view-line > span { background: #0007; }` — a semi-transparent black box behind each text span. All other container levels were already transparent (`#0000`).

**Fix:** Changed `#0007` to `#0000` (fully transparent).

### Fix 3: Enable vim in terminal prompt

**Architecture change across 4 files:**

**MonacoCore.tsx:**
- Vim init now runs for both `mode === 'editor'` and `mode === 'terminal'`
- Terminal starts in insert mode via `monacoVim.VimMode.Vim.handleKey(vimInstance, 'i')`
- Mode changes exposed via `onVimModeChange` callback prop
- Status bar div always rendered (was editor-only — `initVimMode` needs a DOM node)

**MonacoTerminal.tsx:**
- Holds `currentModeRef = useRef<string>('insert')` tracking current vim mode
- Passes `handleVimModeChange` (memoized) to `MonacoCore`
- Passes `currentModeRef` to `useMonacoTerminal`

**useMonacoTerminal.ts:**
- Accepts optional `currentModeRef` param
- Key handler checks `currentModeRef?.current === 'normal'` — returns early in normal mode, letting vim handle all keys (so `i`/`a`/`o` re-enter insert, `w`/`b`/`dw`/`cw` motions work)
- In insert mode, existing terminal handling runs (Enter submits, backspace with prompt protection, arrows for history, chars via setValue+cursor fix)

**monaco-vim.d.ts:**
- Extended type declarations to expose `VimMode`, `VimModeInstance`, `VimApi` with `handleKey`, `defineEx`, `on`, `dispose`
- Fixed 2 pre-existing TS2339 errors (`Property 'VimMode' does not exist`)

---

## Data Flow Diagram

```
MonacoCore (initVimMode)
  ├─ vimInstance.on('vim-mode-change') → onVimModeChange callback
  │                                        ↓
  │                              MonacoTerminal.handleVimModeChange
  │                                        ↓
  │                              currentModeRef.current = 'insert'|'normal'
  │                                        ↓
  └──────────────────────────────── useMonacoTerminal reads currentModeRef
                                    ├─ 'normal' → return (let vim handle)
                                    └─ 'insert' → intercept Enter/Backspace/Ctrl+C/arrows/chars
```

## Risks & Follow-ups

- **Insert-mode bookkeeping:** Our `setValue`-based char insertion bypasses vim's internal insert-mode state tracking. When the user exits insert mode, vim's `exitInsertMode` may behave unexpectedly. Monitor for cursor glitches on Escape.
- **Backspace in normal mode:** Not intercepted — vim handles it as left-cursor motion. Safe for single-line prompt.
- **Arrow keys:** Intercepted in insert mode for history navigation. This replaces vim's up/down cursor motion within insert mode (acceptable for single-line prompt).
- **Prompt boundary protection:** Backspace in insert mode still prevents deleting past the prompt prefix. In normal mode, vim doesn't expose backspace-as-delete so this is not a concern.
- **`handleKey` reliability:** Starting insert mode via `Vim.handleKey(vimInstance, 'i')` is a runtime dispatch into vim's internal keymap. If monaco-vim's internal API changes, this call may need updating. Wrapped in try/catch with warning fallback.
