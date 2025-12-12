# API Key Schemas

Drop-in schemas for APIs requiring API key authentication.

## Available Schemas

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [anthropic.yaml](anthropic.yaml) | Claude Messages | Chat, Vision, Tools, Token Count | ✅ Complete |

## Coming Soon

| Schema | API | Operations |
|--------|-----|------------|
| `openai.yaml` | OpenAI | GPT, DALL-E, Embeddings, Whisper |
| `sendgrid.yaml` | SendGrid | Email sending, Templates |
| `stripe.yaml` | Stripe | Payments, Customers, Invoices |

---

## Quick Start: Anthropic Claude

### 1. Get API Key
Visit https://console.anthropic.com/settings/keys

### 2. Replace Placeholder
```yaml
{{ANTHROPIC_API_KEY}} → sk-ant-api03-...
```

### 3. Required Headers
```http
x-api-key: {{ANTHROPIC_API_KEY}}
anthropic-version: 2023-06-01
Content-Type: application/json
```

### 4. Example Request
```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "Hello, Claude!"}
    ]
  }'
```

### 5. Available Models

| Model | Use Case | Context |
|-------|----------|---------|
| `claude-sonnet-4-20250514` | Balanced performance | 200K |
| `claude-opus-4-20250514` | Most capable | 200K |
| `claude-haiku-3-5-20241022` | Fast & affordable | 200K |

---

## Authentication Patterns

### Header-based (Most Common)
```yaml
securitySchemes:
  ApiKeyAuth:
    type: apiKey
    in: header
    name: X-API-Key
```

### Bearer Token
```yaml
securitySchemes:
  BearerAuth:
    type: http
    scheme: bearer
```

### Query Parameter
```yaml
securitySchemes:
  ApiKeyAuth:
    type: apiKey
    in: query
    name: api_key
```

## ChatGPT Actions Setup

When using API Key schemas in ChatGPT Actions:

1. Select **"API Key"** authentication type
2. Choose **"Custom"** for header name
3. Enter the header name (e.g., `x-api-key`)
4. Paste your API key (stored securely by OpenAI)

## Security Best Practices

- Never commit API keys to version control
- Use environment variables in local development
- Rotate keys periodically
- Set up usage alerts/limits where available
- Use separate keys for dev/prod environments

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
