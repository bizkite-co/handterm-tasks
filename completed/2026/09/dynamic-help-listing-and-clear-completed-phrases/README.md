---
created_at: 2026-09-27T15:21:46.743956-07:00
---

# Dynamic help listing and clear completed phrases

Two bugs:
1. `help` must list every real command from the command registry, not a stale/hand-maintained list. New commands (and their switches, e.g. `tut -r`) must show up automatically.
2. After a phrase is completed in tutorial/game, nextChars/phrase still briefly shows the old phrase. Clear phrase state on completion so resetPrompt/cancelTimer cannot restore it.

## Completion Criteria

help output is generated from the live command registry (including switches/subcommands) so a newly registered command appears with no extra wiring; completing a tutorial or game phrase clears nextChars/phrase so the old phrase does not flash back.

## Solution

help is generated from CommandRegistry.listCommands() (live registered commands, including switches like `tut -r` and subcommands). Completing a game/tutorial phrase now clears nextChars/phrase state; cancelTimer no longer writes the old phrase back, and TutorialManager hides the completed prompt so resetPrompt cannot flash it.

---
**Completed in commit:** `f46c8f4`
