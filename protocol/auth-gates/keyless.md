# Keyless Authentication Gate

## Overview

Keyless access means no authentication required. Public APIs serve data to anyone who asks. This is the simplest and fastest path to API powers.

---

## When to Use Keyless

| Use Case | Example |
|----------|---------|
| Public reference data | Country information, currency codes |
| Open datasets | Government data, Wikipedia |
| Demo/test APIs | JSONPlaceholder, httpbin |
| Free-tier services | Basic weather, joke APIs |

---

## How It Works

```
┌──────────┐                              ┌──────────┐
│  Engine  │                              │ Service  │
│          │                              │   API    │
└────┬─────┘                              └────┬─────┘
     │                                         │
     │  Request (no credentials)               │
     │  GET /api/countries                    │
     │────────────────────────────────────────▶│
     │                                         │
     │                                         │ No auth check
     │                                         │ Serve public data
     │                                         │
     │  Response                               │
     │◀────────────────────────────────────────│
     │                                         │
```

No tokens. No keys. No OAuth dance. Just call it.

---

## OpenAPI Schema Pattern

```yaml
openapi: "3.1.0"
info:
  title: Public API
  version: "1.0.0"

servers:
  - url: https://api.example.com

paths:
  /countries:
    get:
      operationId: listCountries
      summary: Get all countries
      security: []  # Explicitly no security
      responses:
        '200':
          description: List of countries
```

**Key Point**: Use `security: []` to explicitly indicate no authentication.

---

## The CORS Problem

### What is CORS?
Cross-Origin Resource Sharing. Browsers block requests from one domain to another unless the target server explicitly allows it.

### When CORS Matters

| Platform | CORS Issue? |
|----------|-------------|
| ChatGPT Actions | No - server-side calls |
| Google Apps Script | No - server-side calls |
| Google Colab | No - server-side calls |
| Node.js/Python | No - server-side calls |
| **Browser JavaScript** | **YES** |

### CORS-Friendly APIs
These APIs include proper CORS headers:
```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS
```

### Workarounds for Browser
1. Use a CORS proxy
2. Call from server-side instead
3. Use JSONP (legacy)

---

## Popular Keyless APIs

### Reference Data
| API | URL | Description |
|-----|-----|-------------|
| REST Countries | `restcountries.com/v3.1/` | Country information |
| IP-API | `ip-api.com/json/` | IP geolocation |
| Open Library | `openlibrary.org/api/` | Book data |

### Placeholder/Test
| API | URL | Description |
|-----|-----|-------------|
| JSONPlaceholder | `jsonplaceholder.typicode.com/` | Fake REST API |
| httpbin | `httpbin.org/` | HTTP testing |
| ReqRes | `reqres.in/api/` | Test user data |

### Weather (Free Tier)
| API | URL | Description |
|-----|-----|-------------|
| Open-Meteo | `api.open-meteo.com/v1/` | Weather forecasts |
| WTTR.in | `wttr.in/` | Weather (text/JSON) |

### Fun/Utility
| API | URL | Description |
|-----|-----|-------------|
| Dog CEO | `dog.ceo/api/` | Random dog images |
| Cat Facts | `catfact.ninja/` | Random cat facts |
| Joke API | `official-joke-api.appspot.com/` | Random jokes |

---

## Example Schema: Open-Meteo Weather

```yaml
openapi: "3.1.0"
info:
  title: Open-Meteo Weather API
  version: "1.0.0"
  description: |
    Free weather API. No authentication required.
    
    Auth type: keyless
    ChatGPT compatible: yes

servers:
  - url: https://api.open-meteo.com/v1

paths:
  /forecast:
    get:
      operationId: getWeatherForecast
      summary: Get weather forecast
      security: []
      parameters:
        - name: latitude
          in: query
          required: true
          schema:
            type: number
          example: 52.52
        - name: longitude
          in: query
          required: true
          schema:
            type: number
          example: 13.41
        - name: current_weather
          in: query
          schema:
            type: boolean
            default: true
      responses:
        '200':
          description: Weather forecast data
          content:
            application/json:
              schema:
                type: object
                properties:
                  current_weather:
                    type: object
                    properties:
                      temperature:
                        type: number
                      windspeed:
                        type: number
```

---

## Rate Limiting (Still Applies)

Even keyless APIs have limits:

| API | Limit | Notes |
|-----|-------|-------|
| Open-Meteo | 10,000/day | Per IP |
| REST Countries | Unlimited | Fair use |
| IP-API | 45/minute | Per IP |

### Handling Rate Limits
```yaml
responses:
  '429':
    description: Too many requests
    headers:
      Retry-After:
        schema:
          type: integer
```

---

## Web Scraping (Structured Keyless)

When there's no API, schema can define scraping patterns:

```yaml
# Conceptual - for custom engines
info:
  title: Web Scraping Schema
  description: Define data extraction patterns

paths:
  /scrape:
    post:
      operationId: scrapeWebPage
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                url:
                  type: string
                selectors:
                  type: object
                  properties:
                    title:
                      type: string
                      description: CSS selector for title
                    content:
                      type: string
                      description: CSS selector for content
```

**Note**: Web scraping requires a custom engine that executes the selectors. Standard OpenAPI engines won't handle this.

---

## ChatGPT Actions Keyless Setup

1. In GPT Builder, go to Actions
2. Select "None" for authentication
3. Paste your schema
4. Test immediately

No credentials needed. Schema executes directly.

---

## Security Considerations

### Public APIs Are Still APIs
- Respect rate limits
- Don't abuse free services
- Cache responses when appropriate
- Check terms of service

### Data Sensitivity
- Keyless doesn't mean anonymous
- Your IP is logged
- Requests may be monitored
- Don't send sensitive data to unknown APIs

---

## Comparison with Other Gates

| Aspect | Keyless | API Key | OAuth2 |
|--------|---------|---------|--------|
| Setup time | Zero | Minutes | Hours |
| User interaction | None | None | Required |
| Data access | Public only | Service data | User data |
| Rate limits | Strict | Moderate | Generous |
| Cost | Free | Often paid | Often paid |

**Rule of thumb**: Start keyless, upgrade when you need private data.

---

*This gate is the fastest path from schema to execution. Zero friction.*
