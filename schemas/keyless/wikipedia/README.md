# Wikipedia API Schema

Free, keyless access to Wikipedia content via the REST API.

## No Authentication Required

This API is completely public - no API keys, no OAuth, no signup.

## Available Operations

| Operation | Endpoint | Description |
|-----------|----------|-------------|
| `searchArticles` | `GET /page/search/title` | Search for articles |
| `getPageSummary` | `GET /page/summary/{title}` | Get article summary & extract |
| `getPageContent` | `GET /page/mobile-sections/{title}` | Get full article content |
| `getPageLead` | `GET /page/mobile-sections-lead/{title}` | Get intro section only |
| `getPageMedia` | `GET /page/media-list/{title}` | Get images/videos/audio |
| `getRelatedPages` | `GET /page/related/{title}` | Get related articles |
| `getRandomArticle` | `GET /page/random/summary` | Get random article |
| `getOnThisDay` | `GET /feed/onthisday/{type}/{mm}/{dd}` | Historical events |
| `getFeaturedContent` | `GET /feed/featured/{yyyy}/{mm}/{dd}` | Featured content |
| `getMostRead` | `GET /feed/most-read/{yyyy}/{mm}/{dd}` | Most read articles |

## Quick Examples

### Search Articles
```bash
curl "https://en.wikipedia.org/api/rest_v1/page/search/title?q=quantum%20computing&limit=5"
```

### Get Article Summary
```bash
curl "https://en.wikipedia.org/api/rest_v1/page/summary/Albert_Einstein"
```

Response:
```json
{
  "title": "Albert Einstein",
  "description": "German-born theoretical physicist (1879–1955)",
  "extract": "Albert Einstein was a German-born theoretical physicist...",
  "thumbnail": {
    "source": "https://upload.wikimedia.org/...",
    "width": 207,
    "height": 240
  },
  "content_urls": {
    "desktop": {
      "page": "https://en.wikipedia.org/wiki/Albert_Einstein"
    }
  }
}
```

### Get "On This Day" Events
```bash
# July 4th events
curl "https://en.wikipedia.org/api/rest_v1/feed/onthisday/all/07/04"
```

### Get Random Article
```bash
curl "https://en.wikipedia.org/api/rest_v1/page/random/summary"
```

### Get Most Read Articles
```bash
# Yesterday's most read
curl "https://en.wikipedia.org/api/rest_v1/feed/most-read/2024/12/11"
```

## Multi-Language Support

Simply change the domain to access different language Wikipedias:

| Language | Base URL |
|----------|----------|
| English | `https://en.wikipedia.org/api/rest_v1` |
| Spanish | `https://es.wikipedia.org/api/rest_v1` |
| German | `https://de.wikipedia.org/api/rest_v1` |
| French | `https://fr.wikipedia.org/api/rest_v1` |
| Japanese | `https://ja.wikipedia.org/api/rest_v1` |
| Chinese | `https://zh.wikipedia.org/api/rest_v1` |
| Russian | `https://ru.wikipedia.org/api/rest_v1` |
| Portuguese | `https://pt.wikipedia.org/api/rest_v1` |
| Italian | `https://it.wikipedia.org/api/rest_v1` |
| Arabic | `https://ar.wikipedia.org/api/rest_v1` |

## ChatGPT Actions Setup

1. Import `wikipedia.yaml` as your schema
2. No authentication needed - select "None"
3. Test with: "Search Wikipedia for quantum computing"

## Best Practices

1. **Set User-Agent**: Include a descriptive User-Agent header
2. **Cache Responses**: Articles don't change frequently
3. **Respect Rate Limits**: ~200 requests/second max
4. **Handle Redirects**: Many titles redirect to canonical names

## Use Cases

- **Research Assistant**: Search and summarize topics
- **Daily Facts**: "On this day" historical events
- **Random Learning**: Random article exploration
- **Content Enrichment**: Add Wikipedia context to apps
- **Image Sourcing**: Get Creative Commons images

## License

All Wikipedia content is available under:
- [Creative Commons Attribution-ShareAlike 4.0](https://creativecommons.org/licenses/by-sa/4.0/)

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
