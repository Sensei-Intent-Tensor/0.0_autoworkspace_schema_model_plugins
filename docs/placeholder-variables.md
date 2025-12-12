# Placeholder Variables Reference

Complete list of standard `{{PLACEHOLDER}}` variables used across all schemas.

## Authentication Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{CLIENT_ID}}` | OAuth2 client identifier | `123456-abc.apps.googleusercontent.com` |
| `{{CLIENT_SECRET}}` | OAuth2 client secret | `GOCSPX-abc123xyz` |
| `{{CALLBACK_URL}}` | OAuth2 redirect URI | `https://chat.openai.com/aip/g-xxx/oauth/callback` |
| `{{API_KEY}}` | API key for key-based auth | `sk-abc123...` |
| `{{ACCESS_TOKEN}}` | Pre-obtained access token | `ya29.a0AfH6...` |
| `{{REFRESH_TOKEN}}` | OAuth2 refresh token | `1//0abc123...` |

## Scope Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{SCOPE}}` | Single OAuth scope | `https://www.googleapis.com/auth/calendar` |
| `{{SCOPES}}` | Space-separated scopes | `calendar gmail.send drive.file` |

## Endpoint Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{BASE_URL}}` | API base URL | `https://www.googleapis.com` |
| `{{API_VERSION}}` | API version | `v3` |

## Resource Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{USER_ID}}` | User identifier | `me` |
| `{{CALENDAR_ID}}` | Calendar identifier | `primary` |
| `{{SPREADSHEET_ID}}` | Google Sheets ID | `1BxiMVs0XRA5nFMdKvBd...` |

## Platform-Specific Callback URLs

| Platform | Pattern |
|----------|---------|
| ChatGPT (chat.openai.com) | `https://chat.openai.com/aip/g-{GPT_ID}/oauth/callback` |
| ChatGPT (chatgpt.com) | `https://chatgpt.com/aip/g-{GPT_ID}/oauth/callback` |
| Google Apps Script | `https://script.google.com/macros/d/{SCRIPT_ID}/usercallback` |

## Usage

In your schema:
```yaml
authorizationUrl: https://accounts.google.com/o/oauth2/v2/auth
tokenUrl: https://oauth2.googleapis.com/token
# User fills in these values when configuring their engine
```

In your config:
```json
{
  "CLIENT_ID": "your-actual-client-id",
  "CLIENT_SECRET": "your-actual-secret",
  "CALLBACK_URL": "your-platform-callback-url"
}
```
