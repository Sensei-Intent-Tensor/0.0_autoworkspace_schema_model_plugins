# OAuth2 Authentication Gate

## Overview

OAuth2 is the most powerful and complex authentication pattern. It allows users to grant limited access to their data without sharing passwords.

---

## When to Use OAuth2

| Use Case | Example |
|----------|---------|
| Accessing user data | Read someone's calendar events |
| Acting on user's behalf | Send email as the user |
| User-specific resources | Access their Drive files |
| Consent-required operations | Anything needing explicit permission |

---

## The Authorization Code Flow

This is the standard flow for web applications and ChatGPT Actions.

```
┌──────────┐                              ┌──────────┐
│   User   │                              │ Service  │
│          │                              │ (Google) │
└────┬─────┘                              └────┬─────┘
     │                                         │
     │  1. User clicks "Connect"               │
     │────────────────────────────────────────▶│
     │                                         │
     │  2. Redirect to login + consent         │
     │◀────────────────────────────────────────│
     │                                         │
     │  3. User logs in, grants permission     │
     │────────────────────────────────────────▶│
     │                                         │
     │  4. Redirect to callback with ?code=    │
     │◀────────────────────────────────────────│
     │                                         │
┌────┴─────┐                              ┌────┴─────┐
│  Engine  │  5. Exchange code for token  │ Service  │
│          │─────────────────────────────▶│          │
│          │                              │          │
│          │  6. Return access_token +    │          │
│          │     refresh_token            │          │
│          │◀─────────────────────────────│          │
└──────────┘                              └──────────┘
```

---

## Required Components

### 1. Authorization URL
Where users are sent to authenticate.

```
https://accounts.google.com/o/oauth2/v2/auth
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
```

### 2. Token URL
Where codes are exchanged for tokens.

```
https://oauth2.googleapis.com/token
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

### 3. Client Credentials
- **Client ID**: Public identifier for your app
- **Client Secret**: Private key (never expose to users)

### 4. Callback URL
Where the service redirects after authorization.

```
# ChatGPT pattern
https://chat.openai.com/aip/g-{GPT_ID}/oauth/callback

# Apps Script pattern
https://script.google.com/macros/d/{SCRIPT_ID}/usercallback
```

### 5. Scopes
Permissions your app is requesting.

```
# Narrow scope (preferred)
https://www.googleapis.com/auth/calendar.events

# Broad scope (requires verification)
https://www.googleapis.com/auth/calendar
```

---

## OpenAPI Schema Pattern

```yaml
components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://accounts.google.com/o/oauth2/v2/auth
          tokenUrl: https://oauth2.googleapis.com/token
          scopes:
            {{SCOPE}}: {{SCOPE_DESCRIPTION}}

security:
  - OAuth2:
      - {{SCOPE}}
```

---

## Token Management

### Access Token
- Short-lived (typically 1 hour)
- Included in every API request
- `Authorization: Bearer {access_token}`

### Refresh Token
- Long-lived (until revoked)
- Used to get new access tokens
- **Never send to API endpoints**

### Token Refresh Flow
```javascript
// When access_token expires (401 response)
const response = await fetch(tokenUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: new URLSearchParams({
    grant_type: 'refresh_token',
    refresh_token: stored.refresh_token,
    client_id: CLIENT_ID,
    client_secret: CLIENT_SECRET
  })
});

const { access_token, expires_in } = await response.json();
// Store new access_token, keep same refresh_token
```

---

## Common Issues & Solutions

### "Invalid redirect URI"
**Problem**: Callback URL doesn't match exactly.

**Solution**: 
- Copy exact URL from OAuth provider
- Include trailing slashes if present
- Match http vs https exactly

### "Access denied" or "Consent required"
**Problem**: User denied permission or scope changed.

**Solution**:
- Re-initiate OAuth flow
- Add `prompt=consent` to force new consent

### "Token expired"
**Problem**: Access token is no longer valid.

**Solution**:
- Implement automatic token refresh
- Catch 401 errors and refresh

### "Insufficient scopes"
**Problem**: Token doesn't have required permissions.

**Solution**:
- Request all needed scopes upfront
- Use incremental authorization if available

---

## Google OAuth2 Specifics

### Authorization URL Parameters
```
https://accounts.google.com/o/oauth2/v2/auth?
  client_id={{CLIENT_ID}}
  &redirect_uri={{CALLBACK_URL}}
  &response_type=code
  &scope={{SCOPE}}
  &access_type=offline        # Get refresh token
  &prompt=consent             # Force consent screen
  &include_granted_scopes=true  # Incremental auth
```

### Required Google Cloud Setup
1. Create project in Google Cloud Console
2. Enable required APIs (Calendar, Gmail, etc.)
3. Configure OAuth consent screen
4. Create OAuth 2.0 credentials (Web application)
5. Add authorized redirect URIs

---

## Microsoft OAuth2 Specifics

### Authorization URL Pattern
```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
  client_id={{CLIENT_ID}}
  &redirect_uri={{CALLBACK_URL}}
  &response_type=code
  &scope={{SCOPE}}
  &response_mode=query
```

### Tenant Options
- `common` - Any Microsoft account
- `organizations` - Work/school accounts only
- `consumers` - Personal accounts only
- `{tenant-id}` - Specific organization

---

## Security Best Practices

1. **Never expose client_secret** in client-side code
2. **Use HTTPS** for all OAuth endpoints
3. **Validate state parameter** to prevent CSRF
4. **Store tokens securely** (encrypted, not localStorage)
5. **Use minimum required scopes**
6. **Implement token rotation** when available
7. **Handle revocation** gracefully

---

## ChatGPT Actions OAuth2 Notes

ChatGPT Actions handle OAuth2 automatically:
- Token storage is managed
- Token refresh is automatic
- User sees "Sign in" button
- No code needed for auth flow

You only provide:
- Client ID
- Client Secret
- Authorization URL
- Token URL
- Scopes

ChatGPT generates the callback URL for you.

---

*This gate enables the most powerful API operations but requires the most setup.*
