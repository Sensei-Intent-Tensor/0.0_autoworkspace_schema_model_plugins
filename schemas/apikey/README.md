# API Key Schemas

Drop-in schemas for APIs requiring API key authentication.

## Available Schemas

| Schema | API | Operations | Size | Status |
|--------|-----|------------|------|--------|
| [anthropic.yaml](anthropic.yaml) | Claude Messages | Chat, Vision, Tools, Token Count | 16 KB | ✅ |
| [sendgrid.yaml](sendgrid.yaml) | SendGrid Email | Send, Templates, Contacts, Validation | 31 KB | ✅ |
| [stripe.yaml](stripe.yaml) | Stripe Payments | Customers, Payments, Subscriptions, Invoices | 51 KB | ✅ |
| [openai.yaml](openai.yaml) | OpenAI | GPT, Embeddings, Images | 22 KB | ✅ |
| [google-search.yaml](google-search.yaml) | Google Custom Search | Web Search, Image Search | 15 KB | ✅ |
| [bing-search.yaml](bing-search.yaml) | Bing Web Search | Web, News, Images, Videos | 22 KB | ✅ |

---

## 🔍 Search APIs

### Google Custom Search
**Free Tier:** 100 queries/day

```bash
curl "https://www.googleapis.com/customsearch/v1?key=API_KEY&cx=SEARCH_ENGINE_ID&q=machine+learning"
```

**Setup:**
1. Create Custom Search Engine: https://cse.google.com/
2. Get API key: https://console.cloud.google.com/
3. Enable "Search entire web" in CSE settings

### Bing Web Search  
**Free Tier:** 1,000 transactions/month

```bash
curl -H "Ocp-Apim-Subscription-Key: YOUR_KEY" \
  "https://api.bing.microsoft.com/v7.0/search?q=machine+learning"
```

**Setup:**
1. Create Azure account
2. Create Bing Search resource in Azure Portal
3. Get subscription key from Keys and Endpoint

---

## 🤖 AI APIs

### Anthropic Claude

```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model": "claude-sonnet-4-20250514", "max_tokens": 1024, "messages": [{"role": "user", "content": "Hello!"}]}'
```

**Get API Key:** https://console.anthropic.com/settings/keys

### OpenAI

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "gpt-4", "messages": [{"role": "user", "content": "Hello!"}]}'
```

**Get API Key:** https://platform.openai.com/api-keys

---

## 📧 Email API

### SendGrid

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

**Get API Key:** https://app.sendgrid.com/settings/api_keys

---

## 💳 Payments API

### Stripe

```bash
# Create a customer
curl https://api.stripe.com/v1/customers \
  -u "$STRIPE_SECRET_KEY:" \
  -d email="customer@example.com"

# Create a payment intent
curl https://api.stripe.com/v1/payment_intents \
  -u "$STRIPE_SECRET_KEY:" \
  -d amount=2000 \
  -d currency=usd
```

**Get API Key:** https://dashboard.stripe.com/apikeys

**Note:** Stripe uses `application/x-www-form-urlencoded`, not JSON.

---

## Authentication Patterns

| API | Auth Type | Header/Field |
|-----|-----------|--------------|
| Anthropic | API Key (Custom) | `x-api-key` |
| SendGrid | Bearer Token | `Authorization: Bearer` |
| Stripe | Bearer Token | `Authorization: Bearer` |
| OpenAI | Bearer Token | `Authorization: Bearer` |
| Google Search | Query Parameter | `key` |
| Bing Search | Custom Header | `Ocp-Apim-Subscription-Key` |

---

## ChatGPT Actions Setup

| API | Auth Type | Configuration |
|-----|-----------|---------------|
| Anthropic | API Key | Custom header: `x-api-key` |
| SendGrid | API Key | Bearer token |
| Stripe | API Key | Bearer token |
| OpenAI | API Key | Bearer token |
| Google Search | API Key | Query parameter: `key` |
| Bing Search | API Key | Custom header: `Ocp-Apim-Subscription-Key` |

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
