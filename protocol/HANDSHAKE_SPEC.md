# Universal Handshake Protocol Specification

## The Authentication Contract

This document defines the universal handshake protocol that all schema engines must implement. When an engine is "handshake compliant," any schema from this library will work without modification.

---

## Protocol Overview

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   CLIENT    │────▶│   ENGINE    │────▶│   API       │
│  (Schema)   │     │ (Handshake) │     │  (Service)  │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       │  1. Load Schema   │                   │
       │──────────────────▶│                   │
       │                   │                   │
       │  2. Replace {{}}  │                   │
       │──────────────────▶│                   │
       │                   │                   │
       │                   │  3. Auth Flow     │
       │                   │──────────────────▶│
       │                   │                   │
       │                   │  4. Token         │
       │                   │◀──────────────────│
       │                   │                   │
       │  5. Execute       │  6. API Call      │
       │──────────────────▶│──────────────────▶│
       │                   │                   │
       │  8. Response      │  7. Response      │
       │◀──────────────────│◀──────────────────│
```

---

## The Three Auth Gates

Every API interaction passes through one of three gates:

### Gate 1: OAuth2 (User Authorization)
User grants permission for the engine to act on their behalf.

```
Authorization Flow:
1. Engine redirects user to authorizationUrl
2. User authenticates with service (e.g., Google login)
3. User grants permission (consent screen)
4. Service redirects to {{CALLBACK_URL}} with code
5. Engine exchanges code for access_token at tokenUrl
6. Engine stores tokens securely
7. Engine includes access_token in API requests
```

### Gate 2: API Key (Service Authorization)
Pre-shared key authorizes the engine to call the API.

```
Key Flow:
1. User provides {{API_KEY}}
2. Engine includes key in header or query parameter
3. Service validates key
4. Request proceeds or fails
```

### Gate 3: Keyless (Public Access)
No authorization required. Public data only.

```
Keyless Flow:
1. Engine makes request
2. Service responds
(No authentication step)
```

---

## Engine Requirements

A compliant engine MUST:

### 1. Parse OpenAPI 3.1.0 Schemas
- Read `openapi`, `info`, `servers`, `paths`, `components`
- Extract `securitySchemes` definitions
- Map `operationId` to callable functions

### 2. Replace {{PLACEHOLDER}} Variables
- Accept user configuration for all placeholders
- Substitute at runtime before requests
- Never expose secrets in logs

### 3. Implement Security Schemes

#### For OAuth2:
```javascript
// Pseudocode
function initiateOAuth(schema) {
  const authUrl = schema.components.securitySchemes.OAuth2
    .flows.authorizationCode.authorizationUrl;
  
  const params = {
    client_id: config.CLIENT_ID,
    redirect_uri: config.CALLBACK_URL,
    scope: config.SCOPE,
    response_type: 'code',
    access_type: 'offline'  // For refresh tokens
  };
  
  redirect(authUrl + '?' + encode(params));
}

function handleCallback(code) {
  const tokenUrl = schema.components.securitySchemes.OAuth2
    .flows.authorizationCode.tokenUrl;
  
  const tokens = post(tokenUrl, {
    code: code,
    client_id: config.CLIENT_ID,
    client_secret: config.CLIENT_SECRET,
    redirect_uri: config.CALLBACK_URL,
    grant_type: 'authorization_code'
  });
  
  store(tokens);
}

function refreshToken() {
  // Called when access_token expires
  const newTokens = post(tokenUrl, {
    refresh_token: stored.refresh_token,
    client_id: config.CLIENT_ID,
    client_secret: config.CLIENT_SECRET,
    grant_type: 'refresh_token'
  });
  
  store(newTokens);
}
```

#### For API Key:
```javascript
function addApiKey(request, schema) {
  const keyDef = schema.components.securitySchemes.ApiKeyAuth;
  
  if (keyDef.in === 'header') {
    request.headers[keyDef.name] = config.API_KEY;
  } else if (keyDef.in === 'query') {
    request.params[keyDef.name] = config.API_KEY;
  }
  
  return request;
}
```

### 4. Execute Operations
- Map `operationId` to HTTP method + path
- Build request from parameters (path, query, header, body)
- Handle pagination via `pageToken` patterns
- Parse responses according to schema

### 5. Handle Errors
- Token expiration → refresh and retry
- Rate limits → exponential backoff
- Auth failures → re-initiate OAuth flow
- Network errors → surface to user

---

## Token Storage Patterns

Each platform has its own secure storage:

| Platform | Storage Method |
|----------|---------------|
| ChatGPT Actions | Managed by OpenAI |
| Google Apps Script | PropertiesService |
| Google Colab | Credentials file / environment |
| Node.js | Environment variables / keyring |
| Browser | Secure httpOnly cookies (not localStorage) |

---

## Scope Management

When a schema requires multiple scopes:

```yaml
# In schema
scopes:
  https://www.googleapis.com/auth/calendar: Calendar access
  https://www.googleapis.com/auth/gmail.send: Send email
```

The engine MUST:
1. Concatenate scopes with spaces for OAuth request
2. Request all scopes in single authorization
3. Handle partial consent (user denies some scopes)

---

## Multi-Schema Bundling

When combining multiple APIs in one schema:

```yaml
# workspace-bundle.yaml
servers:
  - url: https://www.googleapis.com
    description: Primary Google APIs

paths:
  /calendar/v3/calendars/{calendarId}/events:
    # Calendar endpoints
  /gmail/v1/users/{userId}/messages:
    # Gmail endpoints
  /drive/v3/files:
    # Drive endpoints

components:
  securitySchemes:
    OAuth2:
      flows:
        authorizationCode:
          scopes:
            # All scopes for all services
            https://www.googleapis.com/auth/calendar: Calendar
            https://www.googleapis.com/auth/gmail.send: Gmail
            https://www.googleapis.com/auth/drive.file: Drive
```

---

## Reference Implementations

See `/protocol/engine-reference/` for platform-specific implementations:

- `chatgpt-actions.md` - How ChatGPT implements this protocol
- `google-apps-script.md` - GAS implementation patterns
- `google-colab.md` - Python notebook patterns
- `node.md` - Node.js standalone app
- `python.md` - Python standalone app

---

## Compliance Checklist

An engine is **handshake compliant** if it:

- [ ] Parses OpenAPI 3.1.0 schemas
- [ ] Replaces all `{{PLACEHOLDER}}` variables
- [ ] Implements OAuth2 authorization code flow
- [ ] Implements API key header/query injection
- [ ] Handles keyless (no-auth) requests
- [ ] Manages token refresh automatically
- [ ] Provides secure token storage
- [ ] Surfaces errors appropriately
- [ ] Supports all standard HTTP methods (GET, POST, PUT, PATCH, DELETE)

---

## Intent Tensor Theory Alignment

This protocol embodies the **permissioned collapse** principle:

- **Φ (Intent)**: The schema defines what you want to do
- **Ψ (State)**: The engine manages authentication state
- **ΔΨ (Gap)**: Difference between "want to call API" and "authorized to call API"
- **Collapse**: Execution occurs when auth gate permits

The handshake is the mechanism that collapses intent into action.

---

*Protocol Version: 1.0.0*
*Last Updated: 2025-12-12*
