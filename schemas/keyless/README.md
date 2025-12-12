# Keyless Schemas

Drop-in schemas for public APIs requiring no authentication.

**Zero friction. Just call it.**

## Available Schemas

| Category | Schema | File | Status |
|----------|--------|------|--------|
| Weather | Open-Meteo | `weather/open-meteo.yaml` | ✅ Complete |
| Reference | REST Countries | `reference/countries.yaml` | 🔜 Coming |
| Reference | Wikipedia | `reference/wikipedia.yaml` | 🔜 Coming |
| Placeholder | JSONPlaceholder | `public-data/jsonplaceholder.yaml` | 🔜 Coming |

## Folder Structure

```
/keyless/
├── /weather/          # Weather APIs
├── /reference/        # Reference data (countries, currencies, etc.)
└── /public-data/      # Open datasets and test APIs
```

## Benefits of Keyless APIs

- No registration required
- Instant usage
- Great for prototyping
- Perfect for learning

## Considerations

- Rate limits still apply (per IP)
- Data may be cached
- No user-specific data
- Check terms of service

See `/protocol/auth-gates/keyless.md` for details.
