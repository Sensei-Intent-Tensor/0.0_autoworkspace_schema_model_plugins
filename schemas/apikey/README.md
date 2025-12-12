# API Key Schemas

Drop-in schemas for APIs requiring API key authentication.

## Available Schemas

| Schema | API | Operations | Size | Status |
|--------|-----|------------|------|--------|
| [anthropic.yaml](anthropic.yaml) | Claude Messages | Chat, Vision, Tools, Token Count | 16 KB | ✅ Complete |
| [sendgrid.yaml](sendgrid.yaml) | SendGrid Email | Send, Templates, Contacts, Validation | 31 KB | ✅ Complete |
| [stripe.yaml](stripe.yaml) | Stripe Payments | Customers, Payments, Subscriptions, Invoices | 44 KB | ✅ Complete |
| [openai.yaml](openai.yaml) | OpenAI | GPT, Embeddings, Images | 22 KB | ✅ Complete |

---

## Quick Start: Anthropic Claude

**Get API Key:** https://console.anthropic.com/settings/keys

```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model": "claude-sonnet-4-20250514", "max_tokens": 1024, "messages": [{"role": "user", "content": "Hello!"}]}'
```

---

## Quick Start: SendGrid

**Get API Key:** https://app.sendgrid.com/settings/api_keys

```bash
curl https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [{"to": [{"email": "recipient@example.com"}]}],
    "from": {"email": "sender@yourdomain.com"},
    "subject": "Hello!",
    "content": [{"type": "text/html", "value": "<h1>Hello!</h1>"}]
  }'
```

---

## Quick Start: Stripe

**Get API Key:** https://dashboard.stripe.com/apikeys

```bash
# Create a customer
curl https://api.stripe.com/v1/customers \
  -u "$STRIPE_SECRET_KEY:" \
  -d email="customer@example.com" \
  -d name="John Doe"

# Create a payment intent
curl https://api.stripe.com/v1/payment_intents \
  -u "$STRIPE_SECRET_KEY:" \
  -d amount=2000 \
  -d currency=usd

# Create a subscription
curl https://api.stripe.com/v1/subscriptions \
  -u "$STRIPE_SECRET_KEY:" \
  -d customer=cus_xxx \
  -d "items[0][price]"=price_xxx
```

**Note:** Stripe uses `application/x-www-form-urlencoded` for POST requests, not JSON.

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

### HTTP Basic (Stripe)
```yaml
# Stripe uses Basic Auth with API key as username
# curl -u "sk_xxx:"
securitySchemes:
  BearerAuth:
    type: http
    scheme: bearer
```

---

## ChatGPT Actions Setup

| API | Auth Type | Header/Field |
|-----|-----------|--------------|
| Anthropic | API Key (Custom) | `x-api-key` |
| SendGrid | API Key (Bearer) | `Authorization` |
| Stripe | API Key (Bearer) | `Authorization` |
| OpenAI | API Key (Bearer) | `Authorization` |

---

## Security Best Practices

- Never commit API keys to version control
- Use environment variables locally
- Rotate keys periodically
- Set up usage alerts/limits
- Use separate keys for dev/prod
- Use test keys during development (Stripe: `sk_test_...`)

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
