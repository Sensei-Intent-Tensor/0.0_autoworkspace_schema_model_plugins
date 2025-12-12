# ChatGPT Actions Engine Reference

## How ChatGPT Implemented the Handshake Protocol

ChatGPT Actions is the gold standard for schema engines. OpenAI built a complete OAuth2/API Key/Keyless handshake system that any schema can plug into. This document explains how they did it—so you can understand the pattern and replicate it elsewhere.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     ChatGPT Actions                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Schema    │  │    Auth     │  │  Executor   │        │
│  │   Parser    │──▶│   Manager   │──▶│             │        │
│  │             │  │             │  │             │        │
│  │ OpenAPI 3.1 │  │ OAuth2/Key  │  │ HTTP Client │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│         │                │                │                │
│         ▼                ▼                ▼                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │              Unified Function Interface              │  │
│  │                                                     │  │
│  │   GPT calls: listCalendarEvents(calendarId="primary")│  │
│  │   Engine: GET /calendars/primary/events + Bearer    │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## What ChatGPT Does Automatically

### 1. Schema Parsing
- Reads OpenAPI 3.1.0 YAML/JSON
- Extracts all `operationId` values
- Maps operations to callable functions
- Generates function signatures from parameters

### 2. Authentication Management
- Handles OAuth2 authorization code flow
- Manages token storage (encrypted)
- Automatically refreshes expired tokens
- Injects credentials into requests

### 3. Request Execution
- Builds HTTP requests from operation definitions
- Substitutes path parameters
- Adds query parameters
- Includes request bodies
- Sets authentication headers

### 4. Response Handling
- Parses JSON responses
- Handles errors gracefully
- Presents data to GPT for reasoning

---

## Supported Authentication Types

### OAuth2 (Authorization Code)
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
            https://www.googleapis.com/auth/calendar: Calendar access
```

**ChatGPT UI**: User sees "Sign in with Google" button.

### API Key
```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      name: X-API-Key
      in: header
```

**ChatGPT UI**: Creator enters key in builder; users don't see it.

### Bearer Token
```yaml
components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
```

**ChatGPT UI**: Creator enters token in builder.

### No Auth
```yaml
paths:
  /public-endpoint:
    get:
      security: []
```

**ChatGPT UI**: No authentication prompt.

---

## OAuth2 Flow in ChatGPT

### Step 1: Creator Configuration
In GPT Builder → Actions → Authentication:
- Select "OAuth"
- Enter Client ID
- Enter Client Secret
- Enter Authorization URL
- Enter Token URL
- Enter Scopes

### Step 2: Callback URL Generation
ChatGPT generates unique callback URLs:
```
https://chat.openai.com/aip/g-{GPT_ID}/oauth/callback
https://chatgpt.com/aip/g-{GPT_ID}/oauth/callback
```

**Critical**: Both URLs must be registered in your OAuth provider.

### Step 3: User Authorization
When user triggers an action requiring OAuth:
1. GPT shows "Sign in" button
2. User clicks → redirects to provider login
3. User authorizes → redirects back with code
4. ChatGPT exchanges code for tokens
5. Tokens stored securely

### Step 4: Automatic Token Refresh
- ChatGPT monitors token expiration
- Refreshes automatically before expiry
- User never sees re-authorization

---

## Schema Requirements for ChatGPT

### Must Have
```yaml
openapi: "3.1.0"  # Required version

info:
  title: "Your API Title"
  version: "1.0.0"

servers:
  - url: https://api.example.com  # Single root URL

paths:
  /your-endpoint:
    get:
      operationId: uniqueOperationId  # REQUIRED - becomes function name
```

### Recommended
```yaml
paths:
  /your-endpoint:
    get:
      operationId: listItems
      summary: Get all items          # Helps GPT understand purpose
      description: |                  # Detailed guidance for GPT
        Returns a list of items.
        Use this when user asks about their items.
      parameters:
        - name: limit
          in: query
          description: Maximum items to return  # Parameter guidance
          schema:
            type: integer
            default: 10
```

### Limitations
- **Single server URL**: Can't switch between endpoints
- **45-second timeout**: Long operations fail
- **~100KB response limit**: Paginate large datasets
- **No streaming**: Must be complete responses
- **No file uploads**: Request bodies only

---

## OperationId Best Practices

The `operationId` becomes the function GPT calls. Name it well.

### Good Names
```yaml
operationId: listCalendarEvents     # Clear verb + noun
operationId: createNewTask          # Action-oriented
operationId: searchContactsByName   # Descriptive of filtering
operationId: getWeatherForecast     # Domain-specific clarity
```

### Bad Names
```yaml
operationId: get1                   # Meaningless
operationId: doThing                # Vague
operationId: endpoint_handler       # Implementation detail
operationId: api_call               # Too generic
```

---

## Error Handling

ChatGPT handles errors gracefully:

```yaml
responses:
  '200':
    description: Success
  '400':
    description: Bad request - invalid parameters
  '401':
    description: Unauthorized - will trigger re-auth
  '403':
    description: Forbidden - insufficient permissions
  '404':
    description: Not found
  '429':
    description: Rate limited - GPT will inform user
  '500':
    description: Server error
```

GPT reads error responses and explains them to users naturally.

---

## Multi-API Schemas

Combine multiple APIs in one schema:

```yaml
servers:
  - url: https://www.googleapis.com

paths:
  # Calendar API
  /calendar/v3/calendars/{calendarId}/events:
    get:
      operationId: listCalendarEvents
      
  # Gmail API  
  /gmail/v1/users/{userId}/messages:
    get:
      operationId: listGmailMessages
      
  # Drive API
  /drive/v3/files:
    get:
      operationId: listDriveFiles

components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          scopes:
            https://www.googleapis.com/auth/calendar: Calendar
            https://www.googleapis.com/auth/gmail.readonly: Gmail
            https://www.googleapis.com/auth/drive.readonly: Drive
```

**Note**: All APIs must share the same base server URL structure.

---

## Testing Your Schema

### In GPT Builder
1. Paste schema in Actions
2. Click "Test" next to any operation
3. Enter sample parameters
4. Verify response

### Debug Tips
- Check "View Raw" for actual HTTP request/response
- Verify operationIds match expected function names
- Test OAuth flow with real account
- Check scope permissions match API requirements

---

## Replicating This Engine

To build a ChatGPT Actions-equivalent engine:

### Core Components Needed
1. **OpenAPI Parser**: Read and validate schemas
2. **Credential Store**: Securely store OAuth tokens/API keys
3. **OAuth Flow Handler**: Authorization code exchange
4. **Token Refresher**: Monitor and refresh tokens
5. **HTTP Client**: Execute requests with auth headers
6. **Response Parser**: Extract data from responses

### Minimum Implementation
```python
class SchemaEngine:
    def __init__(self, schema_yaml, credentials):
        self.schema = parse_openapi(schema_yaml)
        self.credentials = credentials
        self.tokens = {}
    
    def call(self, operation_id, **params):
        operation = self.find_operation(operation_id)
        auth = self.get_auth_header(operation)
        request = self.build_request(operation, params)
        response = self.execute(request, auth)
        return self.parse_response(response)
    
    def get_auth_header(self, operation):
        security = operation.get('security', self.schema.get('security', []))
        if not security:
            return {}
        # Handle OAuth2, API Key, etc.
        return {'Authorization': f'Bearer {self.tokens["access_token"]}'}
```

---

## Key Takeaways

1. **ChatGPT solved the hard parts**: OAuth flow, token management, credential storage
2. **Schemas are portable**: Any OpenAPI 3.1.0 schema works
3. **operationId is critical**: It's how GPT calls your functions
4. **Authentication is abstracted**: Users don't see the complexity
5. **Error handling is built-in**: GPT explains failures naturally

**The genius**: Build the engine once, run any schema forever.

---

*This is the reference implementation. Study it. Replicate it.*
