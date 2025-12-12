# Search Engine APIs

Schemas for web search APIs - both keyless and API key variants.

## Available Schemas

### 🔓 Keyless (No API Key Required)

| Schema | API | Description | Status |
|--------|-----|-------------|--------|
| [duckduckgo.yaml](duckduckgo.yaml) | DuckDuckGo Instant | Instant answers, abstracts, !bangs | ✅ |
| [searxng.yaml](searxng.yaml) | SearXNG | Meta-search (70+ engines) | ✅ |

### 🔑 API Key Required (See `/schemas/apikey/`)

| Schema | API | Free Tier | Status |
|--------|-----|-----------|--------|
| `google-search.yaml` | Google Custom Search | 100 queries/day | ✅ |
| `bing-search.yaml` | Bing Web Search | 1,000/month | ✅ |

---

## DuckDuckGo Instant Answer API

**Best for:** Quick facts, definitions, calculations, Wikipedia abstracts

### What It Returns
- Wikipedia-style abstracts
- Instant answers (math, conversions)
- Related topics
- Disambiguation pages
- !Bang redirects

### What It Does NOT Return
- Full web search results
- Ranked search listings
- Images/videos

### Quick Example
```bash
# Get instant answer
curl "https://api.duckduckgo.com/?q=Albert+Einstein&format=json"

# Calculator
curl "https://api.duckduckgo.com/?q=2%2B2&format=json"

# Definition
curl "https://api.duckduckgo.com/?q=define+serendipity&format=json"
```

### Response Types
| Type | Meaning |
|------|---------|
| `A` | Article - Wikipedia abstract available |
| `D` | Disambiguation - Multiple meanings |
| `C` | Category |
| `N` | Name topic |
| `E` | Exclusive - Instant answer |
| `` | Nothing found |

---

## SearXNG Meta-Search

**Best for:** Actual web search results, privacy-focused, aggregated results

### What Is It?
SearXNG aggregates results from 70+ search engines (Google, Bing, DuckDuckGo, Wikipedia, etc.) without tracking.

### Public Instances
Find reliable instances at: https://searx.space/

Popular options:
- `https://searx.be`
- `https://search.sapti.me`
- `https://searx.tiekoetter.com`

### Quick Example
```bash
# Web search
curl "https://searx.be/search?q=python+tutorial&format=json"

# Image search
curl "https://searx.be/search?q=sunset&categories=images&format=json"

# News search
curl "https://searx.be/search?q=tech+news&categories=news&format=json"

# IT/Programming
curl "https://searx.be/search?q=react+hooks&categories=it&format=json"
```

### Categories
| Category | Use For |
|----------|---------|
| `general` | Web search (default) |
| `images` | Image search |
| `videos` | Video search |
| `news` | News articles |
| `it` | Programming/IT |
| `science` | Scientific papers |
| `files` | File downloads |
| `music` | Music |
| `social media` | Social posts |

### Self-Hosting
For production/unlimited use, self-host: https://docs.searxng.org/

---

## Google Custom Search (API Key)

**Best for:** Google-quality results, production apps

### Setup
1. Create Custom Search Engine: https://cse.google.com/
2. Enable "Search entire web"
3. Get API key: https://console.cloud.google.com/

### Pricing
- **Free:** 100 queries/day
- **Paid:** $5 per 1,000 queries

### Quick Example
```bash
curl "https://www.googleapis.com/customsearch/v1?key=YOUR_KEY&cx=YOUR_CX&q=machine+learning"
```

---

## Bing Web Search (API Key)

**Best for:** Alternative to Google, good image/video search

### Setup
1. Create Azure account
2. Create Bing Search resource in Azure Portal
3. Get subscription key

### Pricing
- **Free (F1):** 1,000 transactions/month
- **Paid (S1):** $7 per 1,000 transactions

### Quick Example
```bash
curl -H "Ocp-Apim-Subscription-Key: YOUR_KEY" \
  "https://api.bing.microsoft.com/v7.0/search?q=machine+learning"
```

### Endpoints
| Endpoint | Description |
|----------|-------------|
| `/search` | Web search |
| `/news/search` | News search |
| `/images/search` | Image search |
| `/videos/search` | Video search |
| `/suggestions` | Autocomplete |

---

## Comparison

| Feature | DDG Instant | SearXNG | Google CSE | Bing |
|---------|-------------|---------|------------|------|
| **Cost** | Free | Free | 100/day free | 1K/mo free |
| **Auth** | None | None | API Key | API Key |
| **Web Results** | ❌ | ✅ | ✅ | ✅ |
| **Instant Answers** | ✅ | ✅ | ❌ | ✅ |
| **Images** | ❌ | ✅ | ✅ | ✅ |
| **Videos** | ❌ | ✅ | ❌ | ✅ |
| **News** | ❌ | ✅ | ❌ | ✅ |
| **Rate Limit** | Gentle | Instance-dependent | 100/day | 3/sec |

---

## ChatGPT Actions Recommendations

| Use Case | Recommended API |
|----------|-----------------|
| Quick facts & definitions | DuckDuckGo Instant |
| Full web search (free) | SearXNG |
| Production web search | Google CSE or Bing |
| Image search | SearXNG or Bing |
| News search | SearXNG or Bing |

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
