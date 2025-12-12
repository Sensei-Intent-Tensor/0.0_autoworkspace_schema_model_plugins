# Schema Specification Standard

## The `{{PLACEHOLDER}}` Convention

All schemas in this repository use double-curly-brace placeholders for user-configurable values. This ensures schemas are **portable across any engine** without modification to the core structure.

---

## Standard Placeholders

### Authentication Placeholders

| Placeholder | Description | Example Value |
|-------------|-------------|---------------|
| `{{CLIENT_ID}}` | OAuth2 client identifier | `123456789-abc.apps.googleusercontent.com` |
| `{{CLIENT_SECRET}}` | OAuth2 client secret | `GOCSPX-abc123...` |
| `{{CALLBACK_URL}}` | OAuth redirect URI | `https://chat.openai.com/aip/g-xxx/oauth/callback` |
| `{{API_KEY}}` | API key for key-based auth | `sk-abc123...` |
| `{{ACCESS_TOKEN}}` | Pre-obtained access token | `ya29.a0AfH6...` |
| `{{REFRESH_TOKEN}}` | OAuth refresh token | `1//0abc123...` |

### Scope Placeholders

| Placeholder | Description | Example Value |
|-------------|-------------|---------------|
| `{{SCOPE}}` | Single OAuth scope | `https://www.googleapis.com/auth/calendar` |
| `{{SCOPES}}` | Space-separated scopes | `calendar gmail.send drive.file` |
| `{{SCOPE_DESCRIPTION}}` | Human-readable scope name | `Full calendar access` |

### Endpoint Placeholders

| Placeholder | Description | Example Value |
|-------------|-------------|---------------|
| `{{BASE_URL}}` | API base URL | `https://www.googleapis.com` |
| `{{API_VERSION}}` | API version string | `v3` |
| `{{RESOURCE_ID}}` | Dynamic resource identifier | `primary` or `calendarId` |
| `{{USER_ID}}` | User identifier | `me` |

### Configuration Placeholders

| Placeholder | Description | Example Value |
|-------------|-------------|---------------|
| `{{TIMEZONE}}` | User timezone | `America/New_York` |
| `{{LOCALE}}` | User locale | `en-US` |
| `{{MAX_RESULTS}}` | Pagination limit | `100` |

---

## Usage Patterns

### OAuth2 Security Scheme
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
```

### API Key Security Scheme
```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      name: {{API_KEY_HEADER_NAME}}
      in: header
```

### Server Configuration
```yaml
servers:
  - url: {{BASE_URL}}/{{API_VERSION}}
    description: Production server
```

### Path Parameters
```yaml
paths:
  /calendars/{{CALENDAR_ID}}/events:
    get:
      operationId: listCalendarEvents
```

---

## Platform-Specific Callback URLs

When using OAuth2, each platform generates its own callback URL:

| Platform | Callback URL Pattern |
|----------|---------------------|
| ChatGPT Actions | `https://chat.openai.com/aip/g-{GPT_ID}/oauth/callback` |
| ChatGPT (chatgpt.com) | `https://chatgpt.com/aip/g-{GPT_ID}/oauth/callback` |
| Google Apps Script | `https://script.google.com/macros/d/{SCRIPT_ID}/usercallback` |
| Custom App | Your app's redirect endpoint |

---

## Validation Rules

1. **All placeholders must use double curly braces**: `{{NAME}}`
2. **Placeholder names must be SCREAMING_SNAKE_CASE**
3. **No spaces inside braces**: `{{CLIENT_ID}}` not `{{ CLIENT_ID }}`
4. **Document all placeholders** in schema comments or description
5. **Provide example values** in accompanying documentation

---

## Schema File Naming Convention

```
{service}.yaml           # Single service schema
{service}-{subset}.yaml  # Partial service schema
{service}-bundle.yaml    # Multi-service combined schema
```

Examples:
- `calendar.yaml` - Full Google Calendar API
- `calendar-events.yaml` - Events operations only
- `google-workspace-bundle.yaml` - All Google APIs combined

---

## Required Schema Metadata

Every schema must include:

```yaml
openapi: "3.1.0"
info:
  title: "{Service Name} API Schema"
  version: "1.0.0"
  description: |
    Drop-in schema for {Service Name}.
    
    Required placeholders:
    - {{CLIENT_ID}}: Your OAuth client ID
    - {{CLIENT_SECRET}}: Your OAuth client secret
    
    Auth type: oauth2 | apikey | keyless
    ChatGPT compatible: yes | no
  contact:
    name: Auto-Workspace-AI
    url: https://auto-workspace-ai.com
```

---

## Extending the Standard

To propose new standard placeholders:

1. Open an issue with the proposed placeholder
2. Explain the use case
3. Provide example implementations
4. Wait for review and approval

The goal is consistency across all schemas in this library.
