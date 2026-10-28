# Transparent Background & Video Mode Styling

## 1. Problem
When recording demo videos using `video` mode (`isShowVideoSignal`), the webcam video element (`<video id="terminal-video">`) is placed behind the terminal elements (`z-index: -1`).
In normal terminal mode, the background was transparent (`#0000`), with `#0007` semitransparent rectangles behind text elements, letting the video show cleanly through empty space.
However, when switching to:
- **File Browser (`BrowseView`)**: The `.browse-view` container had a solid dark background (`#111`), `.browse-preview-col` had `#0d0d0d`, `.browse-col-path` had `#181b20`, and `.browse-prompt-bar` had `#1a1c23`, completely obscuring the webcam video.
- **VIM Editor Mode (`MonacoCore`)**: In `mode === 'editor'`, Monaco was set to the `vs-dark` theme with an opaque background `#1e1e1e`, blocking the entire screen. Furthermore, `div.view-line > span` was transparent (`#0000`), lacking the `#0007` text backing.

## 2. Solution Implemented
1. **Monaco Transparent Theme across Modes**:
   - Updated `MonacoCore.tsx` to use `'handterm-transparent'` for both `mode === 'terminal'` and `mode === 'editor'`.
   - Set `editor.background: '#00000000'` and `editorGutter.background: '#00000000'`.
   - Styled `div.view-line > span` with `background: #0007; display: inline-block;` so each line of code has a semitransparent dark rectangle behind its text, leaving empty lines and right padding completely transparent.
   - Styled `.monaco-editor .line-numbers` with `background-color: #0007;`.
   - Styled `.vim-status-bar` and its spans/inputs with `background-color: #0007;` and `background: transparent;`.
   - Styled `pre#prompt-header span` with `background-color: #0007; display: inline-block;`.

2. **BrowseView Transparency & Text Rectangles**:
   - Updated `BrowseView.css` to make `.browse-view`, `.browse-hint`, `.browse-columns`, `.browse-col`, `.browse-col-path`, `.browse-row`, `.browse-empty`, `.browse-preview-col`, and `.browse-prompt-bar` fully `background: transparent;`.
   - Added `background-color: #0007; display: inline-block;` behind:
     - `.browse-hint span` (help instructions and status).
     - `.browse-col-path span` (breadcrumb paths).
     - `.browse-row span` (file/directory icons and names).
     - `.browse-empty span` (empty and loading messages).
     - `.browse-preview-content` (previews rendered as inline-block rectangles).
     - `.browse-prompt-label` and `.browse-prompt-input`.
   - Updated `.browse-row.selected span` to highlight with `background-color: #2a4a6add;` behind the text and icon rather than drawing an opaque bar across the full screen.

## 3. Verification
- All 44 test suites (224 tests) passed.
- Production build succeeded (`npm run build`).
- Changes committed in `ffd5842`.
