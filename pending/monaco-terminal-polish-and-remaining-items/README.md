---
created_at: 2026-07-25T10:57:27.920634-07:00
---

# monaco-terminal polish and remaining items

## Remaining items from monaco-terminal refactor

### Verified done in latest commit (9bb0db9):
- ✅ Transparent background via custom Monaco theme
- ✅ Vim enabled in terminal (insert mode start, Alt+S exit)
- ✅ Prompt shows on first load
- ✅ Focus border removed
- ✅ Font matches terminal (Fira Code, 16px)
- ✅ Prompt-header and timestamp styled
- ✅ App margin (10px / 5px mobile)
- ✅ Monaco container no padding
- ✅ Effect 5 value sync restricted to editor mode

### Needs verification:
- `div.monaco-scrollable-element` left offset — was 16px, added `folding: false` to try to reach 0px. Verify in browser.
- `pre#prompt-header` selector — PromptHeader uses `id='prompt-header'`, CSS uses `pre#prompt-header`. Verify it applies.
- Editor mode still works (theme switches back to vs-dark, value sync works, vim commands defined)

### Potential future work:
- Consider lifting VIM_ESCAPE_KEY_COMBO to localStorage/signal-based setting
- Consider making prompt customizable (currently hardcoded TERMINAL_CONSTANTS.PROMPT)
- Monaco editor mode CSS cleanup — some terminal-specific overrides may leak

## Completion Criteria

Left offset is 0px, prompt displays on first load, all CSS overrides verified working, no regressions in editor mode
