# API Key Authentication Gate

## Overview

API Key authentication is the simplest form of authorization. A pre-shared secret grants access to the API. No user interaction required.

---

## When to Use API Keys

| Use Case | Example |
|----------|---------|
| Service-to-service calls | OpenAI API, SendGrid |
| Rate-limited public APIs | Weather services |
| Developer-specific access | Personal API usage |
| No user context needed | Data lookup services |

---

## How It Works

```
┌──────────┐                              ┌──────────┐
│  Engine  │                              │ Service  │
│          │                              │   API    │
└────┬─────┘                              └────┬─────┘
     │                                         │
     │  Request + API Key                      │
     │  Header: X-API-Key: sk-abc123          │
     │────────────────────────────────────────▶│
     │                                         │
     │                                         │ Validate key
     │                                         │ Check rate limits
     │                                         │ Log usage
     │                                         │
     │  Response                               │
     │◀────────────────────────────────────────│
     │                                         │
```

---

## Key Transmission Methods

### 1. Header (Recommended)
```http
GET /api/data HTTP/1.1
Host: api.example.com
X-API-Key: {{API_KEY}}
```

Or with Authorization header:
```http
Authorization: Bearer {{API_KEY}}
```

### 2. Query Parameter (Less Secure)
```http
GET /api/data?api_key={{API_KEY}} HTTP/1.1
Host: api.example.com
```

**Warning**: Query parameters may be logged in server logs, browser history, and referrer headers.

---

## OpenAPI Schema Patterns

### Header-Based Key
```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      name: X-API-Key
      in: header

security:
  - ApiKeyAuth: []
```

### Bearer Token Style
```yaml
components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer

security:
  - BearerAuth: []
```

### Query Parameter Key
```yaml
components:
  securitySchemes:
    ApiKeyQuery:
      type: apiKey
      name: api_key
      in: query

security:
  - ApiKeyQuery: []
```

---

## Common API Key Patterns by Service

### OpenAI
```yaml
securitySchemes:
  OpenAI:
    type: http
    scheme: bearer
# Usage: Authorization: Bearer sk-...
```

### SendGrid
```yaml
securitySchemes:
  SendGrid:
    type: http
    scheme: bearer
# Usage: Authorization: Bearer SG...
```

### Generic X-API-Key
```yaml
securitySchemes:
  ApiKey:
    type: apiKey
    name: X-API-Key
    in: header
```

### RapidAPI
```yaml
securitySchemes:
  RapidAPI:
    type: apiKey
    name: X-RapidAPI-Key
    in: header
```

---

## Placeholder Pattern

In schemas:
```yaml
# The engine replaces {{API_KEY}} at runtime
servers:
  - url: https://api.openai.com/v1

components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      # Note: The actual key is provided by user configuration
      # Schema uses: Authorization: Bearer {{API_KEY}}
```

User configuration:
```json
{
  "API_KEY": "sk-proj-abc123..."
}
```

---

## Rate Limiting

Most API key services implement rate limits:

| Service | Rate Limit | Window |
|---------|------------|--------|
| OpenAI | Varies by tier | Per minute |
| SendGrid | 100-1000/day | Daily |
| Weather APIs | 60-1000/day | Daily |

### Handling Rate Limits
```yaml
responses:
  '429':
    description: Rate limit exceeded
    headers:
      Retry-After:
        schema:
          type: integer
        description: Seconds until retry allowed
      X-RateLimit-Remaining:
        schema:
          type: integer
        description: Requests remaining in window
```

---

## Security Considerations

### DO:
- Store keys in environment variables
- Use HTTPS for all requests
- Rotate keys periodically
- Use scoped keys when available
- Monitor key usage

### DON'T:
- Commit keys to version control
- Share keys across applications
- Use keys in client-side code
- Log keys in plaintext
- Use query parameter transmission

---

## ChatGPT Actions API Key Setup

1. In GPT Builder, go to Actions
2. Select "API Key" authentication
3. Choose "Basic" or "Bearer"
4. Enter your API key
5. ChatGPT injects it automatically

```
Auth Type: API Key
API Key: sk-abc123...
Auth Header: Authorization
Auth Header Value: Bearer {{API_KEY}}
```

---

## Multiple Keys

Some APIs require multiple keys:

```yaml
components:
  securitySchemes:
    ApiKey:
      type: apiKey
      name: X-API-Key
      in: header
    AppId:
      type: apiKey
      name: X-App-Id
      in: header

security:
  - ApiKey: []
    AppId: []
```

Placeholder configuration:
```json
{
  "API_KEY": "secret-key-123",
  "APP_ID": "app-456"
}
```

---

## Key Rotation

When rotating keys:

1. Generate new key from service dashboard
2. Update configuration with new key
3. Test with new key
4. Revoke old key
5. Monitor for failures

Never have a period with no valid key.

---

## Comparison with OAuth2

| Aspect | API Key | OAuth2 |
|--------|---------|--------|
| Setup complexity | Low | High |
| User interaction | None | Required |
| Access scope | Full key permissions | Granular scopes |
| Revocation | Regenerate key | Revoke token |
| User-specific data | No | Yes |
| Refresh mechanism | N/A | Refresh tokens |

**Rule of thumb**: Use API keys for service access, OAuth2 for user data.

---

*This gate is the fastest path from schema to execution.*
