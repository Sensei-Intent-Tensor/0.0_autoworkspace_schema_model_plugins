# API Key Schemas

Drop-in schemas for APIs requiring API key authentication.

## Available Schemas

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [anthropic.yaml](anthropic.yaml) | Claude Messages | Chat, Vision, Tools, Token Count | ✅ Complete |
| [sendgrid.yaml](sendgrid.yaml) | SendGrid Email | Send, Templates, Contacts, Validation | ✅ Complete |

## Coming Soon

| Schema | API | Operations |
|--------|-----|------------|
| `openai.yaml` | OpenAI | GPT, DALL-E, Embeddings, Whisper |
| `stripe.yaml` | Stripe | Payments, Customers, Invoices |

---

## Quick Start: Anthropic Claude

### Get API Key
https://console.anthropic.com/settings/keys

### Headers
```http
x-api-key: {{ANTHROPIC_API_KEY}}
anthropic-version: 2023-06-01
```

### Example
```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

---

## Quick Start: SendGrid

### Get API Key
https://app.sendgrid.com/settings/api_keys

### Headers
```http
Authorization: Bearer {{SENDGRID_API_KEY}}
Content-Type: application/json
```

### Example: Send Email
```bash
curl https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [{"to": [{"email": "recipient@example.com"}]}],
    "from": {"email": "sender@yourdomain.com"},
    "subject": "Hello from SendGrid!",
    "content": [{"type": "text/html", "value": "<h1>Hello!</h1>"}]
  }'
```

### Example: With Dynamic Template
```bash
curl https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [{
      "to": [{"email": "customer@example.com"}],
      "dynamic_template_data": {
        "first_name": "Sarah",
        "order_id": "12345"
      }
    }],
    "from": {"email": "orders@yourstore.com"},
    "template_id": "d-xxxxxxxxxxxx"
  }'
```

---

## Authentication Patterns

### Bearer Token (SendGrid, OpenAI)
```yaml
securitySchemes:
  BearerAuth:
    type: http
    scheme: bearer
```

### Custom Header (Anthropic)
```yaml
securitySchemes:
  ApiKeyAuth:
    type: apiKey
    in: header
    name: x-api-key
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

1. Select **"API Key"** authentication type
2. Choose **"Bearer"** or **"Custom"** based on API
3. For custom headers, enter header name (e.g., `x-api-key`)
4. Paste your API key (stored securely)

## Security Best Practices

- Never commit API keys to version control
- Use environment variables locally
- Rotate keys periodically
- Set up usage alerts/limits
- Use separate keys for dev/prod

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
