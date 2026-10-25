---
created_at: 2026-10-25T14:23:29.752762-07:00
---

# Fix regression where tutorial nextChars renders undefined.svg for semicolon and colon

### Summary
The tutorial chord display for the `jkl;` step renders `<div class="col-sm-2 row generated next" id="chord2"><span id="char15" class="char"></span><img alt="2" src="/images/svgs/undefined.svg" height="59.516" width="75" class="hand"></div>` instead of the hand chord glyph for the `;` character.

### Root Cause & Regression Analysis
The regression was introduced in commit `1f910ea2e2221d855fa3b4e895d89646463c90fb` ("feat: align chars glyphs with flashed firmware chords").

During the refresh of `src/allChords.ts`:
- At index 46, `"key": ";"` was replaced with `"key": ""` (`chordCode: "9"`).
- The shifted colon entry `{ "key": ":", "chordCode": "59", "index": 46 }` was completely removed.

When `TutorialManager.tsx` renders the tutorial phrase `jkl;` (`packages/types/src/phrases.ts`), it instantiates `<Chord displayChar=";" />`. In `src/components/Chord.tsx`, `allChords.find(x => x.key === displayChar)` fails because there is no entry with `key === ";"`. As a result, `foundChord` is `undefined`, causing `foundChar` to be empty and `glyphCode` to be `undefined`, generating `/images/svgs/undefined.svg`.

### Proposed Fix
1. Update `src/allChords.ts` at index 46 to set `"key": ";"` for `chordCode: "9"`.
2. Re-add the shifted colon entry `{ "key": ":", "chordCode": "59", "index": 46 }`.
3. Add a test in `src/commands/__tests__/` or a dedicated test verifying all curriculum phrase characters (including `;`) resolve to valid chords in `allChords`.

## Completion Criteria

1. `src/allChords.ts` contains `{ "key": ";", "chordCode": "9", "index": 46 }` and `{ "key": ":", "chordCode": "59", "index": 46 }`.
2. `<Chord displayChar=";" />` renders `9.svg` with `;` character span instead of `undefined.svg`.
3. `<Chord displayChar=":" />` renders `59.svg` with `:` character span.
4. Automated test verifies that all characters in tutorial phrases resolve to defined chord glyphs without missing/undefined entries.
