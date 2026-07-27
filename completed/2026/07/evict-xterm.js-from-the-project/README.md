---
created_at: 2026-07-25T11:31:45.562540-07:00
---

# Evict xterm.js from the project

## Dead xterm files to remove

| File | Status |
|---|---|
| `src/hooks/useTerminal.ts` | Old terminal hook — imports @xterm/addon-fit, react-xtermjs, @xterm/xterm |
| `src/components/XtermAdapterConfig.ts` | Config for xterm adapter |
| `src/assets/terminal.css` | Has `.xterm .xterm-viewport`, `.xterm-screen`, `.xterm-rows` rules |

## Dead test files to remove/update
- `src/hooks/useTerminal.test.ts` — tests for old hook
- `src/e2e/page-objects/TerminalPage.ts` — uses `#xtermRef`
- `src/test-utils/TerminalTestUtils.ts` — uses `xtermRef` test ID
- `src/test-utils/setup.ts` — mocks for xterm, @xterm/addon-fit
- `src/components/HandTermWrapper.test.tsx` — references xtermRef element

## Package.json cleanup
Remove from dependencies/devDependencies:
- `@xterm/xterm`
- `@xterm/addon-fit`
- `react-xtermjs`

## Steps
1. Verify useMonacoTerminal has feature parity with useTerminal before removing
2. Remove dead files
3. Clean up CSS (remove .xterm rules)
4. Update test mocks and utilities
5. Remove packages from package.json
6. Run build to verify no broken imports

## Completion Criteria

No xterm imports, no @xterm packages in package.json, no XtermAdapterConfig.ts, no xterm CSS, all tests pass

## Solution

Removed all xterm.js dead code and packages. Deleted: useTerminal.ts, useTerminal.test.ts, XtermAdapterConfig.ts. Removed xterm CSS rules (.xterm-viewport, .xterm-screen, .xterm-rows, .xterm-cursor-block). Removed @xterm/xterm, @xterm/addon-fit, react-xtermjs from package.json. Removed from vite.config.ts optimizeDeps. Updated TerminalTestUtils.ts, TerminalPage.ts (e2e), HandTermWrapper.test.tsx to remove xterm references. Updated setup.ts test mocks to remove xterm mocks. All pre-existing TS errors remain, no new errors introduced.

---
**Completed in commit:** `f041f80`
