# Social Platform APIs

Keyless access to social platforms and community sites.

## Available Schemas

| Schema | Platform | Operations | Auth | Status |
|--------|----------|------------|------|--------|
| [reddit.yaml](reddit.yaml) | Reddit | Posts, Comments, Users, Search | None | ✅ |
| [hackernews.yaml](hackernews.yaml) | Hacker News | Stories, Comments, Users | None | ✅ |

## Coming Soon

| Schema | Platform | Notes |
|--------|----------|-------|
| `mastodon.yaml` | Mastodon | Public timeline, toots |
| `lemmy.yaml` | Lemmy | Federated Reddit alternative |
| `lobsters.yaml` | Lobsters | Tech news community |

---

## Reddit JSON API

### How It Works
Add `.json` to any Reddit URL:
```
reddit.com/r/programming → reddit.com/r/programming.json
```

### Quick Examples
```bash
# Get subreddit posts
curl "https://www.reddit.com/r/programming.json?limit=10"

# Get top posts this week
curl "https://www.reddit.com/r/programming/top.json?t=week&limit=25"

# Search Reddit
curl "https://www.reddit.com/search.json?q=machine%20learning&sort=top&t=month"

# Get post with comments
curl "https://www.reddit.com/r/programming/comments/abc123.json"

# Get user profile
curl "https://www.reddit.com/user/spez/about.json"

# Multi-subreddit
curl "https://www.reddit.com/r/python+javascript+rust.json"
```

### Key Endpoints
| Endpoint | Description |
|----------|-------------|
| `/r/{sub}.json` | Subreddit posts (hot) |
| `/r/{sub}/top.json?t=week` | Top posts by time |
| `/r/{sub}/new.json` | Newest posts |
| `/r/{sub}/comments/{id}.json` | Post + comments |
| `/user/{name}/about.json` | User profile |
| `/search.json?q=query` | Global search |

---

## Hacker News API

### How It Works
Official Firebase API - completely public and documented.

### Quick Examples
```bash
# Get top story IDs
curl "https://hacker-news.firebaseio.com/v0/topstories.json"

# Get story details
curl "https://hacker-news.firebaseio.com/v0/item/39234567.json"

# Get user profile
curl "https://hacker-news.firebaseio.com/v0/user/pg.json"

# Get Ask HN stories
curl "https://hacker-news.firebaseio.com/v0/askstories.json"

# Get Show HN stories
curl "https://hacker-news.firebaseio.com/v0/showstories.json"

# Get job postings
curl "https://hacker-news.firebaseio.com/v0/jobstories.json"
```

### Story Lists
| Endpoint | Description |
|----------|-------------|
| `/topstories.json` | Front page (500 IDs) |
| `/newstories.json` | Newest stories |
| `/beststories.json` | Highest voted |
| `/askstories.json` | Ask HN posts |
| `/showstories.json` | Show HN posts |
| `/jobstories.json` | Job postings |

### Fetching Full Stories
The story lists return IDs only. To get full details:
```javascript
// 1. Get top story IDs
const ids = await fetch('https://hacker-news.firebaseio.com/v0/topstories.json').then(r => r.json());

// 2. Fetch each story (first 10)
const stories = await Promise.all(
  ids.slice(0, 10).map(id => 
    fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json`).then(r => r.json())
  )
);
```

---

## ChatGPT Actions Setup

Both APIs work with zero configuration:

1. Import the YAML schema
2. Select **"None"** for authentication
3. Test with: "Show me the top posts on r/programming" or "What's trending on Hacker News?"

---

## Rate Limits & Best Practices

### Reddit
- ~60 requests/minute (unofficial)
- Set descriptive User-Agent header
- May get 429 if too aggressive
- NSFW content filtered without auth

### Hacker News
- No published limits
- Be reasonable (~1 req/sec)
- Cache story details (they don't change often)
- Consider batching item fetches

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
