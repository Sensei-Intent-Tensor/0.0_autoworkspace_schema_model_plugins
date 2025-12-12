# Keyless / Public API Schemas

Drop-in schemas for completely free, public APIs requiring no authentication.

## Available Schemas

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [weather/open-meteo.yaml](weather/open-meteo.yaml) | Open-Meteo | Weather, Forecasts, Air Quality | ✅ Complete |
| [wikipedia/wikipedia.yaml](wikipedia/wikipedia.yaml) | Wikipedia | Search, Summaries, On This Day | ✅ Complete |

## Coming Soon

| Schema | API | Operations |
|--------|-----|------------|
| `rest-countries.yaml` | REST Countries | Country data, Flags, Currencies |
| `github-public.yaml` | GitHub Public | Repos, Users, Gists (unauthenticated) |

---

## Quick Start: Open-Meteo (Weather)

```bash
# Current weather in San Francisco
curl "https://api.open-meteo.com/v1/forecast?latitude=37.77&longitude=-122.42&current=temperature_2m,wind_speed_10m"

# 7-day forecast
curl "https://api.open-meteo.com/v1/forecast?latitude=37.77&longitude=-122.42&daily=temperature_2m_max,temperature_2m_min&timezone=America/Los_Angeles"
```

---

## Quick Start: Wikipedia

```bash
# Search articles
curl "https://en.wikipedia.org/api/rest_v1/page/search/title?q=machine%20learning&limit=5"

# Get article summary
curl "https://en.wikipedia.org/api/rest_v1/page/summary/Artificial_intelligence"

# On this day events
curl "https://en.wikipedia.org/api/rest_v1/feed/onthisday/all/12/12"

# Random article
curl "https://en.wikipedia.org/api/rest_v1/page/random/summary"

# Most read articles yesterday
curl "https://en.wikipedia.org/api/rest_v1/feed/most-read/2024/12/11"
```

---

## Why Keyless APIs?

**Zero friction integration:**
- No signup required
- No API keys to manage
- No rate limit tiers to worry about
- No billing surprises

**Perfect for:**
- Prototyping and MVPs
- Educational projects
- Personal tools
- Public data enrichment
- ChatGPT Actions (simplest setup)

---

## ChatGPT Actions Setup

For keyless APIs, setup is trivial:

1. Import the schema YAML
2. Select **"None"** for authentication
3. Test immediately

No OAuth configuration, no API key storage, no callback URLs.

---

## Rate Limit Guidelines

Even though these APIs are free, be respectful:

| API | Guideline |
|-----|-----------|
| Open-Meteo | 10,000 requests/day (non-commercial) |
| Wikipedia | ~200 requests/second |
| REST Countries | Unlimited (be reasonable) |
| GitHub Public | 60 requests/hour (unauthenticated) |

---

## Other Notable Keyless APIs

APIs we may add schemas for in the future:

| Category | APIs |
|----------|------|
| **Data** | JSONPlaceholder, REST Countries, IP-API |
| **Reference** | Wikipedia, Open Library, Dictionary API |
| **Science** | NASA APOD, USGS Earthquake, Open-Meteo |
| **Fun** | Cat Facts, Dog API, Chuck Norris Jokes |
| **Utility** | QR Code Generator, URL Shorteners |
| **Finance** | ExchangeRate-API (limited), CoinGecko |

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
