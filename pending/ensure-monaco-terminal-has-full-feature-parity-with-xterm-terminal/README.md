---
created_at: 2026-07-25T11:31:45.640815-07:00
---

# Ensure Monaco terminal has full feature parity with xterm terminal

## xterm features from useTerminal.ts that need verification in useMonacoTerminal

| Feature | xterm | Monaco status |
|---|---|---|
| write(data) | instance.write(data) | ✅ via setValue + cursor |
| focus() | instance.focus() | ✅ editor.focus() |
| resetPrompt() | instance.reset() + write PROMPT | ✅ setValue(PROMPT) |
| scrollToBottom() | instance.scrollToBottom() | ❓ Need to verify |
| onData(callback) | instance.onData(callback) | ✅ via onKeyDown |
| FitAddon.fit() | fitAddon.fit() | ✅ automaticLayout: true + ResizeObserver |
| cursorBlink | cursorBlink: true | ❓ Monaco default — verify |
| cursorStyle 'block' | cursorStyle: 'block' | ✅ cursorStyle: 'block' |
| Selection | Built-in | ❓ Verify selection works |
| Scroll history | scrollback: 1000 | ❓ Monaco scrollBeyondLastLine: false — verify |
| Theme colors | ANSI palette | ❌ See Monaco config parity task |

## Additional Monaco-specific features to verify
- Word wrap works (wordWrap: 'on')
- Scrollbar hidden (scrollbar: { vertical: 'hidden', horizontal: 'hidden' })
- No minimap
- Context menu disabled
- Quick suggestions disabled
- Hover disabled
- Links disabled

## Steps
1. Test each feature manually in browser
2. Add scrollToBottom equivalent if missing (editor.revealLine(m.getLineCount()))
3. Verify cursor blink is working
4. Verify selection (click+drag or shift+arrows) works

## Completion Criteria

All xterm features replicated: scroll to bottom, cursor blink, selection, ANSI color support, FitAddon-like auto-resize, writeToTerminal
