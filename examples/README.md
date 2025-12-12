# Working Examples

Complete, tested examples showing schemas in action with specific engines.

## Structure

Each example folder contains:
- `schema.yaml` - The schema used
- `config.json` - Configuration template
- `README.md` - Setup instructions
- Engine-specific files

## Available Examples

| Example | Auth | Engine | Status |
|---------|------|--------|--------|
| ChatGPT + Calendar | OAuth2 | ChatGPT Actions | 🔜 Coming |
| GAS + Gmail | OAuth2 | Google Apps Script | 🔜 Coming |
| Colab + Weather | Keyless | Google Colab | 🔜 Coming |
| Node + OpenAI | API Key | Node.js | 🔜 Coming |

## Example Format

```
/examples/chatgpt-calendar-oauth2/
├── README.md           # Step-by-step setup guide
├── schema.yaml         # Copy of the schema
├── config.template.json # Placeholders to fill
└── screenshots/        # Visual setup guide
```

## Contributing Examples

1. Create folder with pattern: `{engine}-{api}-{auth}`
2. Include complete setup instructions
3. Test before submitting
4. Add screenshots where helpful
