---
created_at: 2026-07-28T20:47:54.085881-07:00
---

# Fix Monaco editor test mocks in HandTermWrapper.test.tsx

The test suite fails to resolve the monaco-editor package entry. Likely the vitest mock for monaco-editor needs to be enhanced (e.g., add `defineTheme`, `setTheme`, `onDidCreateEditor`, etc.), or the `server.deps.inline` / `optimizeDeps.include` config in vitest setup needs adjustment. Current mock at `src/test-utils/setup.ts` lines 52-77 only provides `editor.create`. See also MonacoCore.tsx line 19 which calls `monaco.editor.defineTheme` at module scope.

## Completion Criteria

HandTermWrapper.test.tsx runs tests (not 0 tests) without module resolution errors for monaco-editor
