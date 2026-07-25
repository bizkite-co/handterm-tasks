---
created_at: 2026-07-25T11:31:45.474608-07:00
---

# Fix edit mode navigation (?activity=edit not working)

## Problem
User reports they can't navigate to edit mode anymore. The edit command is defined in `src/commands/editCommand.tsx` and calls `context.updateLocation({ activityKey: ActivityType.EDIT })`.

## Navigation flow
1. `editCommand.tsx` calls `context.updateLocation({ activityKey: ActivityType.EDIT })`
2. This should dispatch a `locationchange` event with `{ activity: ActivityType.EDIT }`
3. `useActivityMediator` listens for `locationchange` and sets `activitySignal.value`
4. `HandTermWrapper` checks `currentActivityValue === ActivityType.EDIT` to show editor component

## Possible causes
- `context.updateLocation` may not be dispatching the event correctly
- The `useActivityMediator` listener may not be handling the EDIT activity
- `parseLocation()` in App.tsx checks `activityKey !== ActivityType.EDIT` to hide Output — this may be interfering
- MonacoCore editor mode component is memoized with `getStoredContent()` — content may be empty

## Files to investigate
- `src/commands/editCommand.tsx`
- `src/contexts/CommandProvider.tsx` (updateLocation impl)
- `src/hooks/useActivityMediator.ts` (locationchange listener)
- `src/components/HandTermWrapper.tsx` (edit rendering)
- `src/App.tsx` (Output hide logic)
- `src/signals/appSignals.ts` (activity signal)

## Completion Criteria

Typing 'edit' command in terminal navigates to edit activity, Monaco editor renders in editor mode with stored content

## Solution

Root cause: edit command requires Cognito auth. User was not logged in. Login is a two-step interactive process: `login <username>` then type password. After login, edit command works. No code fix needed — the auth flow is working correctly.

---
**Completed in commit:** `6b604c9`
