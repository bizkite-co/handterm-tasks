---
created_at: 2026-10-25T15:50:44.098988-07:00
---

# Respawn hero in game on death with failure indicator

### Goal
When the hero dies in the game (3 zombie hits), the game currently exits to the terminal (`ActivityType.NORMAL`). Instead, the game should reset the hero and level state so the user can easily stay in the game, while showing an indicator that they failed/died once on this attempt.

### Scope
1. Do not navigate to NORMAL on hero death in `Game.tsx`.
2. After the hero's death animation, respawn/reset the hero and zombie back to starting positions, reset scroll offset, restore lives, and clear `commandLineSignal`.
3. Provide a clear failure indicator (e.g. death counter / retry banner / attempt indicator) so the player knows they failed once.
4. Reset or update the failure indicator when advancing to the next level.

## Completion Criteria

1. Hero death does not kick the user out of the game into the normal terminal.
2. Hero and zombie reset back to their starting positions after death.
3. User's command line is cleared and phrase is ready to be typed from the start.
4. A visual failure indicator indicates that the player failed once on the level.
5. Advancing to the next level resets the level failure state appropriately.
6. Unit tests verify the respawn and failure indicator behavior.

## Solution

Instead of exiting the game to the normal terminal (`navigate({ activityKey: ActivityType.NORMAL })`) on hero death, the game now auto-respawns after the death animation (1.5s). The hero, zombie, background scroll, and typed command line reset to the beginning of the level, allowing the player to easily stay in the game and retry. A failure badge (`💀 {count}`) appears in the top-right HUD to indicate that the player has failed on the attempt, and resets whenever the level is completed or reset. Also hooked `onHeroDeath` to reset the terminal prompt and added full unit test coverage.

---
**Completed in commit:** `b77d3ae`
