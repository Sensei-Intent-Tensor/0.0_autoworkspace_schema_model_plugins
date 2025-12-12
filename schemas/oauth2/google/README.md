# Google Workspace OAuth2 Schemas

Production-ready OpenAPI 3.1.0 schemas for Google Workspace APIs.

## Complete Suite

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [calendar.yaml](calendar.yaml) | Calendar v3 | Events, Calendars, Colors, Free/Busy | ✅ Complete |
| [gmail.yaml](gmail.yaml) | Gmail v1 | Messages, Threads, Labels, Drafts, Send | ✅ Complete |
| [drive.yaml](drive.yaml) | Drive v3 | Files, Folders, Permissions, Export | ✅ Complete |
| [docs.yaml](docs.yaml) | Docs v1 | Documents, Batch Updates, Formatting | ✅ Complete |
| [sheets.yaml](sheets.yaml) | Sheets v4 | Spreadsheets, Values, Formatting | ✅ Complete |
| [contacts.yaml](contacts.yaml) | People v1 | Contacts, Groups, Search | ✅ Complete |
| [tasks.yaml](tasks.yaml) | Tasks v1 | Task Lists, Tasks, Subtasks | ✅ Complete |

## Quick Start

### 1. Google Cloud Setup

```bash
# Create project at https://console.cloud.google.com
# Enable APIs:
# - Google Calendar API
# - Gmail API
# - Google Drive API
# - Google Docs API
# - Google Sheets API
# - People API
# - Tasks API
```

### 2. OAuth2 Credentials

1. Go to **APIs & Services** → **Credentials**
2. Create **OAuth 2.0 Client ID**
3. Set application type based on your engine:
   - **Web application** for ChatGPT Actions, web apps
   - **Desktop app** for local scripts
4. Add authorized redirect URIs for your platform

### 3. Replace Placeholders

```yaml
# In any schema, replace:
{{CLIENT_ID}}     → Your OAuth client ID
{{CLIENT_SECRET}} → Your OAuth client secret  
{{CALLBACK_URL}}  → Your platform's callback URL
```

## Common Scopes Reference

### Minimal (Recommended)
```
https://www.googleapis.com/auth/calendar.events      # Calendar events only
https://www.googleapis.com/auth/gmail.readonly       # Read emails only
https://www.googleapis.com/auth/drive.file           # Files opened/created by app
https://www.googleapis.com/auth/documents.readonly   # Read docs only
https://www.googleapis.com/auth/spreadsheets.readonly # Read sheets only
https://www.googleapis.com/auth/contacts.readonly    # Read contacts only
https://www.googleapis.com/auth/tasks.readonly       # Read tasks only
```

### Full Access
```
https://www.googleapis.com/auth/calendar             # Full calendar
https://www.googleapis.com/auth/gmail.modify         # Read/write/send email
https://www.googleapis.com/auth/drive                # Full Drive access
https://www.googleapis.com/auth/documents            # Full Docs access
https://www.googleapis.com/auth/spreadsheets         # Full Sheets access
https://www.googleapis.com/auth/contacts             # Full contacts access
https://www.googleapis.com/auth/tasks                # Full tasks access
```

## Platform-Specific Setup

### ChatGPT Actions

```yaml
# OAuth configuration in ChatGPT:
Authorization URL: https://accounts.google.com/o/oauth2/v2/auth
Token URL: https://oauth2.googleapis.com/token
Scope: [select scopes needed]
Token Exchange: POST
```

**Important:** Add `https://chat.openai.com/aip/*/oauth/callback` to authorized redirect URIs.

### Google Apps Script

```javascript
// Use OAuth2 library: 1B7FSrk5Zi6L1rSxxTDgDEUsPzlukDsi4KGuTMorsTQHhGBzBkMun4iDF
const service = OAuth2.createService('google')
  .setAuthorizationBaseUrl('https://accounts.google.com/o/oauth2/v2/auth')
  .setTokenUrl('https://oauth2.googleapis.com/token')
  .setClientId('{{CLIENT_ID}}')
  .setClientSecret('{{CLIENT_SECRET}}')
  .setCallbackFunction('authCallback')
  .setPropertyStore(PropertiesService.getUserProperties())
  .setScope('https://www.googleapis.com/auth/calendar');
```

### Python (requests-oauthlib)

```python
from requests_oauthlib import OAuth2Session

client_id = "{{CLIENT_ID}}"
client_secret = "{{CLIENT_SECRET}}"
authorization_base_url = "https://accounts.google.com/o/oauth2/v2/auth"
token_url = "https://oauth2.googleapis.com/token"
scope = ["https://www.googleapis.com/auth/calendar"]

google = OAuth2Session(client_id, scope=scope)
authorization_url, state = google.authorization_url(
    authorization_base_url,
    access_type="offline",
    prompt="consent"
)
```

### Node.js (googleapis)

```javascript
const { google } = require('googleapis');

const oauth2Client = new google.auth.OAuth2(
  '{{CLIENT_ID}}',
  '{{CLIENT_SECRET}}',
  '{{CALLBACK_URL}}'
);

const scopes = ['https://www.googleapis.com/auth/calendar'];

const authUrl = oauth2Client.generateAuthUrl({
  access_type: 'offline',
  scope: scopes
});
```

## API Capabilities Matrix

| Feature | Calendar | Gmail | Drive | Docs | Sheets | Contacts | Tasks |
|---------|----------|-------|-------|------|--------|----------|-------|
| List | ✅ | ✅ | ✅ | — | ✅ | ✅ | ✅ |
| Get | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Create | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Update | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Delete | ✅ | ✅ | ✅ | — | — | ✅ | ✅ |
| Search | ✅ | ✅ | ✅ | — | — | ✅ | — |
| Batch | — | ✅ | — | ✅ | ✅ | ✅ | — |
| Share | — | — | ✅ | — | — | — | — |
| Export | — | — | ✅ | — | — | — | — |

## Common Patterns

### Pagination

All list endpoints support pagination:

```yaml
# Request
GET /endpoint?pageToken=xxx&maxResults=100

# Response includes
{
  "nextPageToken": "abc123",
  "items": [...]
}
```

### Error Handling

```json
{
  "error": {
    "code": 403,
    "message": "The caller does not have permission",
    "status": "PERMISSION_DENIED"
  }
}
```

Common error codes:
- `400` - Bad Request (invalid parameters)
- `401` - Unauthorized (token expired)
- `403` - Forbidden (insufficient scope)
- `404` - Not Found
- `429` - Rate Limited

### Rate Limits

| API | Quota |
|-----|-------|
| Calendar | 1,000,000 queries/day |
| Gmail | 250 quota units/user/second |
| Drive | 20,000 queries/100 seconds |
| Docs | 300 read/60 write per minute |
| Sheets | 300 read/60 write per minute |
| People | 90 requests/minute |
| Tasks | 50,000 queries/day |

## Intent Tensor Integration

These schemas embody the **permissioned collapse** principle:

```
Φ (Intent) = Schema-defined API operation
Ψ (State)  = OAuth token + scope permissions  
ΔΨ (Gap)   = Missing scopes or expired token
Collapse   = Successful API call when Φ ∩ Ψ ≠ ∅
```

The schemas define WHAT is possible. The auth state determines WHEN it collapses into execution.

## Resources

- [Google API Console](https://console.cloud.google.com/apis)
- [OAuth 2.0 Playground](https://developers.google.com/oauthplayground/)
- [API Explorer](https://developers.google.com/apis-explorer)
- [Scopes Reference](https://developers.google.com/identity/protocols/oauth2/scopes)

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
