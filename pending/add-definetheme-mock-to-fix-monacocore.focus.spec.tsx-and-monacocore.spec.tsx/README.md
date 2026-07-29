---
created_at: 2026-07-28T20:47:54.292744-07:00
---

# Add defineTheme mock to fix MonacoCore.focus.spec.tsx and MonacoCore.spec.tsx

MonacoCore.tsx calls `monaco.editor.defineTheme('handterm-transparent', ...)` at module scope (line 19). The vitest mock for monaco-editor in `src/test-utils/setup.ts` doesn't include `defineTheme`. Add it. Also check if `setTheme`, `setModelLanguage`, and other Monaco API calls need mocking. These same mock changes will likely also resolve the MonacoCore.q.spec.tsx 'Unknown file extension .css' error.

## Completion Criteria

MonacoCore.focus.spec.tsx and MonacoCore.spec.tsx run tests (not 0 tests) without 'editor.defineTheme is not a function' error
