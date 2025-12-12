# API Key Schemas

Drop-in schemas for APIs requiring API key authentication.

## Folder Structure

```
/apikey/
├── /ai-services/       # AI/ML APIs (OpenAI, Anthropic, etc.)
├── /data-providers/    # Data APIs (financial, news, etc.)
└── /utilities/         # Utility APIs (URL shorteners, etc.)
```

## Coming Soon

- OpenAI API
- Anthropic API
- SendGrid API
- Twilio API
- News APIs
- Financial data APIs

## How API Key Auth Works

API keys are included in requests via:

1. **Header** (recommended): `X-API-Key: your-key`
2. **Bearer**: `Authorization: Bearer your-key`
3. **Query param**: `?api_key=your-key`

See `/protocol/auth-gates/apikey.md` for details.
