---
created_at: 2026-10-25T18:31:36.784340-07:00
---

# Implement vimtut interactive Vim tutorial in Monaco editor

Build an interactive, step-by-step Vim tutorial (`vimtut`) in Handterm using Monaco and `monaco-vim`:

1. Architectural Setup:
   - Define `VimTutorialStep` interface with step metadata: id, title, instructions, initialText, initialCursor, targetCursor, targetText, targetMode, hint, successMessage.
   - Implement `vimtut` command registered in `commandRegistry` to launch the tutorial.
   - Create a dedicated UI view/activity or overlay banner (`VimTutManager` / `VimTutHeader`) showing step instructions and progress.

2. Step Validation Runner:
   - Hook into Monaco editor event emitters: `onDidChangeCursorPosition`, `onDidChangeModelContent`, and `vimInstance.on('vim-mode-change')`.
   - Validate target conditions reactively and transition seamlessly to the next step.

3. Initial Curriculum (Vertical Slice + Core Movements):
   - Step 1: Normal mode navigation (`h`, `j`, `k`, `l`) to target position/word.
   - Step 2: Character deletion (`x`).
   - Step 3: Word deletion (`dw`).
   - Step 4: Text insertion (`i`) and returning to Normal mode (`Esc` / `Alt+S`).
   - Step 5: Word jumping (`w`, `b`, `e`).
   - Step 6: Exiting/saving (`:q`, `:wq`).

4. Persistence:
   - Track progress and completion in localStorage so users can resume or reset with `reset`.

## Completion Criteria

1. `vimtut` command is registered and discoverable in `help`.
2. Running `vimtut` populates Monaco editor with tutorial text, instructions header, and enables Vim mode.
3. Reactively verifies cursor position, text modifications, and Vim modes against step completion criteria.
4. Completing a step automatically advances to the next step with user feedback.
5. Includes comprehensive unit and integration tests with 100% pass rate.
