# API Key Schemas

Drop-in OpenAPI 3.1.0 schemas for APIs that use API Key authentication.

## Available Schemas

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [openai.yaml](openai.yaml) | OpenAI | Chat, Embeddings, Images, Audio, Moderation | ✅ Complete |
| anthropic.yaml | Anthropic Claude | Messages API | ⬜ Pending |
| sendgrid.yaml | SendGrid | Email, Templates | ⬜ Pending |
| stripe.yaml | Stripe | Payments, Customers | ⬜ Pending |

## Authentication Pattern

API Key schemas use Bearer token or custom header authentication:

```yaml
# Bearer Token (OpenAI, Anthropic)
Authorization: Bearer {{API_KEY}}

# Custom Header (SendGrid)
Authorization: Bearer {{SENDGRID_API_KEY}}

# Custom Header (Stripe)
Authorization: Bearer {{STRIPE_SECRET_KEY}}
```

## Quick Start

### 1. Get Your API Key

| Service | Where to Get Key |
|---------|------------------|
| OpenAI | https://platform.openai.com/api-keys |
| Anthropic | https://console.anthropic.com/settings/keys |
| SendGrid | https://app.sendgrid.com/settings/api_keys |
| Stripe | https://dashboard.stripe.com/apikeys |

### 2. Replace Placeholder

```yaml
# In the schema, replace:
{{OPENAI_API_KEY}} → sk-proj-xxxxx...
```

### 3. Use with Your Engine

**ChatGPT Actions:**
```yaml
# Authentication settings:
Type: API Key
Auth Type: Bearer
```

**cURL:**
```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"gpt-4o-mini","messages":[{"role":"user","content":"Hello!"}]}'
```

**Python:**
```python
import requests

headers = {
    "Authorization": f"Bearer {api_key}",
    "Content-Type": "application/json"
}
response = requests.post(url, headers=headers, json=payload)
```

## OpenAI Schema Details

### Endpoints

| Endpoint | Operation | Description |
|----------|-----------|-------------|
| `/chat/completions` | createChatCompletion | Generate chat responses |
| `/embeddings` | createEmbedding | Generate vector embeddings |
| `/images/generations` | createImage | Generate images (DALL·E) |
| `/audio/speech` | createSpeech | Text to speech |
| `/audio/transcriptions` | createTranscription | Speech to text (Whisper) |
| `/models` | listModels | List available models |
| `/moderations` | createModeration | Content moderation |

### Models

| Model | Use Case | Context |
|-------|----------|---------|
| `gpt-4o` | Flagship multimodal | 128K |
| `gpt-4o-mini` | Fast & affordable | 128K |
| `gpt-4-turbo` | Previous flagship | 128K |
| `text-embedding-3-small` | Embeddings | - |
| `text-embedding-3-large` | High-dim embeddings | - |
| `dall-e-3` | Image generation | - |
| `whisper-1` | Speech to text | - |
| `tts-1` / `tts-1-hd` | Text to speech | - |

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
