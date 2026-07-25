# Edit Mode Navigation Investigation

## Root Cause
The `edit` command requires AWS Cognito authentication. It calls `getFile(context.auth, filename)` which uses `auth.validateAndRefreshToken()` to get a Bearer token. Without a valid session, it returns "Authentication failed" and never reaches the `context.updateLocation({ activityKey: ActivityType.EDIT })` call.

## Auth System
- **Provider:** AWS Cognito via custom API Gateway + Lambda backend
- **Login flow:** Two-step interactive terminal process:
  1. `login <username>` → "Enter password:" prompt
  2. Type password → calls `POST /signIn` → stores tokens in localStorage
- **Tokens stored:** AccessToken, RefreshToken, IdToken, ExpiresAt, ExpiresIn, githubUsername
- **GitHub:** Secondary — account linking only via device flow, not login

## Login Command Flow
1. User types `login username` → LoginCommand sets `isInLoginProcessSignal=true`, stores username
2. User types password → terminal detects login process, constructs synthetic `login username password`
3. LoginCommand calls `auth.login(username, password)` → `POST /signIn`
4. On success, tokens stored in localStorage, `isLoggedIn=true`
5. Terminal shows "Login successful!"

## Edit Command Flow (after login)
1. `edit` or `edit <filename>` → calls `getFile(context.auth, filename)`
2. `getFile` calls `auth.validateAndRefreshToken()` → gets AccessToken
3. Makes authenticated `GET /getFile?filename=<name>` request
4. Stores content in localStorage (`StorageKeys.editContent`)
5. Calls `context.updateLocation({ activityKey: ActivityType.EDIT })` → `navigate()` → dispatches `locationchange`
6. `useActivityMediator` listener sets `activitySignal.value = EDIT`
7. `HandTermWrapper` hides terminal, shows editor component

## Status
User needs to log in first. The `edit` command works correctly once authenticated.

## Key Files
- `src/hooks/useAuth.ts` — auth hook with login/signup/verify/refresh
- `src/commands/LoginCommand.ts` — two-step login command
- `src/commands/editCommand.tsx` — edit command (requires auth)
- `src/utils/awsApiClient.ts` — authenticated API client
- `src/shared/endpoints.json` — API endpoints
- `.env` — Cognito config (User Pool, Client ID)
