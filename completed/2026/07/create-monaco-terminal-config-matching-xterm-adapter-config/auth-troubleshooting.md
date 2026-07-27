# Auth Troubleshooting

## User can't log in with new Cognito user

### Common causes:
1. **User not confirmed** — Cognito requires email verification after signup. Status must be `CONFIRMED` in Cognito console.
2. **Password not set** — If user created via Cognito console, password must be set explicitly (Actions → Reset password).
3. **User not in correct group** — App may require user to be in a specific Cognito group.

### API endpoint
- Base: `https://drypicnke5.execute-api.us-east-1.amazonaws.com`
- Sign in: `POST /signIn` with `{ username, password }`
- Expected response: `{ AccessToken, RefreshToken, IdToken, ExpiresIn, githubUsername }`

### Login flow
1. `login <username>` → stores username, shows "Enter password:"
2. Type password → constructs `login <username> <password>`
3. `LoginCommand.execute()` calls `auth.login(username, password)`
4. `useAuth.loginMutation` POSTs to `/signIn`
5. On success: tokens stored in localStorage

### Debugging steps
1. Check DevTools Network tab for `/signIn` response
2. Check Cognito User Pool for user status (CONFIRMED vs UNCONFIRMED)
3. Verify user has a password set
4. Check browser console for errors
