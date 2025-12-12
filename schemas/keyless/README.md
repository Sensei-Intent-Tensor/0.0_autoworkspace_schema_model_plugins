# Keyless (Public) API Schemas

Drop-in schemas for APIs that require **no authentication**.

## Available Schemas

| Schema | API | Operations | Size | Status |
|--------|-----|------------|------|--------|
| [open-meteo.yaml](weather/open-meteo.yaml) | Open-Meteo | Weather, Forecast, Air Quality | — | ✅ Complete |
| [rest-countries.yaml](rest-countries.yaml) | REST Countries | Country data, Search, Regions | 19 KB | ✅ Complete |
| [github-public.yaml](github-public.yaml) | GitHub (Public) | Users, Repos, Gists, Search | 37 KB | ✅ Complete |

## Coming Soon

| Schema | API | Operations |
|--------|-----|------------|
| `wikipedia.yaml` | Wikipedia | Article search, Summaries |

---

## Quick Start: GitHub Public API

**No API key needed!** (60 requests/hour limit)

```bash
# Get user profile
curl "https://api.github.com/users/octocat"

# Get repository info
curl "https://api.github.com/repos/microsoft/vscode"

# List user's repos
curl "https://api.github.com/users/torvalds/repos?sort=updated"

# Get file contents
curl "https://api.github.com/repos/octocat/Hello-World/contents/README.md"

# Search repositories
curl "https://api.github.com/search/repositories?q=language:python+stars:>10000"

# Check rate limit
curl "https://api.github.com/rate_limit"
```

### Available Endpoints

| Category | Operations |
|----------|------------|
| **Users** | `getUser`, `listUserRepos`, `listUserFollowers`, `listUserFollowing`, `listUserGists` |
| **Repositories** | `getRepository`, `getRepoContents`, `getReadme`, `listCommits`, `getCommit`, `listBranches`, `getBranch`, `listStargazers`, `listForks`, `listContributors`, `listLanguages`, `listTopics` |
| **Issues** | `listIssues`, `getIssue` |
| **Releases** | `listReleases`, `getLatestRelease`, `getReleaseByTag` |
| **Gists** | `listPublicGists`, `getGist` |
| **Search** | `searchRepositories`, `searchUsers`, `searchCode`, `searchIssues` |
| **Meta** | `getRateLimit` |

### Search Query Syntax

```bash
# Repositories
?q=language:javascript+stars:>1000
?q=topic:react+pushed:>2024-01-01
?q=user:facebook+fork:true

# Users  
?q=location:san+francisco+followers:>100

# Code
?q=filename:package.json+express

# Issues
?q=repo:facebook/react+is:open+label:bug
```

---

## Quick Start: REST Countries

**No API key needed!**

```bash
# Get all countries
curl "https://restcountries.com/v3.1/all"

# Search by name
curl "https://restcountries.com/v3.1/name/germany"

# Get by region
curl "https://restcountries.com/v3.1/region/europe"

# Filter fields
curl "https://restcountries.com/v3.1/all?fields=name,capital,population,flags"
```

---

## Quick Start: Open-Meteo

**No API key needed!**

```bash
# Weather forecast
curl "https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current_weather=true"

# Geocoding
curl "https://geocoding-api.open-meteo.com/v1/search?name=Berlin"
```

---

## Rate Limits

| API | Unauthenticated Limit |
|-----|----------------------|
| GitHub | 60 requests/hour per IP |
| REST Countries | No official limit (be respectful) |
| Open-Meteo | 10,000 requests/day |

---

## Why Keyless APIs?

Perfect for:
- **Prototyping** - No signup friction
- **Education** - Students can start immediately  
- **Public data** - Weather, geography, open source
- **ChatGPT Actions** - Simplest integration path

---

## ChatGPT Actions Setup

For keyless APIs in ChatGPT Actions:

1. Select **"None"** for authentication
2. Paste the schema YAML
3. Test immediately - no credentials needed!

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
