# Google Workspace OAuth2 Schemas

Drop-in schemas for Google APIs requiring OAuth2 authentication.

## Available Schemas

| Schema | File | Status |
|--------|------|--------|
| Calendar | `calendar.yaml` | ✅ Complete |
| Gmail | `gmail.yaml` | ✅ Complete |
| Drive | `drive.yaml` | 🔜 Coming |
| Docs | `docs.yaml` | 🔜 Coming |
| Sheets | `sheets.yaml` | 🔜 Coming |
| Contacts | `contacts.yaml` | 🔜 Coming |
| Tasks | `tasks.yaml` | 🔜 Coming |

## Required Setup

1. Create project in [Google Cloud Console](https://console.cloud.google.com)
2. Enable the required API
3. Configure OAuth consent screen
4. Create OAuth 2.0 credentials (Web application)
5. Add your platform's callback URLs

## Common Scopes

```
# Calendar
https://www.googleapis.com/auth/calendar
https://www.googleapis.com/auth/calendar.events
https://www.googleapis.com/auth/calendar.readonly

# Gmail
https://www.googleapis.com/auth/gmail.readonly
https://www.googleapis.com/auth/gmail.send
https://www.googleapis.com/auth/gmail.modify

# Drive
https://www.googleapis.com/auth/drive
https://www.googleapis.com/auth/drive.file
https://www.googleapis.com/auth/drive.readonly

# Sheets
https://www.googleapis.com/auth/spreadsheets
https://www.googleapis.com/auth/spreadsheets.readonly

# Docs
https://www.googleapis.com/auth/documents
https://www.googleapis.com/auth/documents.readonly
```
