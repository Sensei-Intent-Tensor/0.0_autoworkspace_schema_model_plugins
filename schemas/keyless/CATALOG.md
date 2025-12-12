# Keyless API Catalog & Roadmap

This document catalogs public APIs available without authentication, organized by category.

## Legend

| Symbol | Meaning |
|--------|---------|
| ✅ | Schema complete |
| 🔨 | In progress |
| ⬜ | Planned |
| 🔑 | Requires free API key |
| ⚠️ | Rate limited / Unofficial |

---

## 📚 REFERENCE & KNOWLEDGE

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| Wikipedia | Articles, search, on-this-day | None | ✅ |
| Open Library | Books, authors, covers | None | ⬜ |
| Dictionary API | Word definitions, phonetics | None | ⬜ |
| Datamuse | Word finding, rhymes, synonyms | None | ⬜ |
| Wikimedia Commons | Free images, media | None | ⬜ |
| Wikidata | Structured knowledge base | None | ⬜ |
| arXiv | Scientific papers | None | ⬜ |
| PubChem | Chemical information | None | ⬜ |

---

## 🔍 SEARCH ENGINES

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| DuckDuckGo Instant | Instant answers, abstracts | None | ✅ |
| DuckDuckGo HTML | Search results (scrape) | None ⚠️ | ⬜ |
| SearXNG | Meta-search (self-host) | None | ✅ |
| Google Custom Search | Web search | 🔑 Free tier | ✅ |
| Bing Search | Web search | 🔑 Free tier | ✅ |

---

## 🗺️ MAPS & LOCATION

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| Nominatim (OSM) | Geocoding, reverse geocoding | None | ⬜ |
| Overpass (OSM) | Map data queries | None | ⬜ |
| IP-API | IP geolocation | None | ⬜ |
| ipinfo.io | IP geolocation | None (limited) | ⬜ |
| Geonames | Place names, postal codes | 🔑 Free | ⬜ |
| Google Maps | Maps, places, directions | 🔑 Free tier | ⬜ |
| Mapbox | Maps, geocoding | 🔑 Free tier | ⬜ |

---

## 🌤️ WEATHER & ENVIRONMENT

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| Open-Meteo | Weather, forecasts, air quality | None | ✅ |
| USGS Earthquake | Earthquake data | None | ⬜ |
| EPA AirNow | Air quality (US) | None | ⬜ |
| Sunrise-Sunset | Sun times by location | None | ⬜ |

---

## 💬 SOCIAL PLATFORMS

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| Reddit (.json) | Posts, comments, subreddits | None ⚠️ | ✅ |
| Hacker News | Tech news, comments | None | ✅ |
| Lobsters | Tech news | None | ⬜ |
| Mastodon | Public toots, trends | None | ⬜ |
| Lemmy | Federated Reddit alternative | None | ⬜ |
| LinkedIn | Profile data | 🔑 OAuth | ⬜ |
| Twitter/X | Tweets, users | 🔑 OAuth | ⬜ |
| Facebook Graph | Pages, posts | 🔑 OAuth | ⬜ |

---

## 💻 DEVELOPER PLATFORMS

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| GitHub Public | Repos, users, gists | None (60/hr) | ✅ |
| GitLab Public | Projects, users | None | ⬜ |
| NPM Registry | Package info | None | ⬜ |
| PyPI | Python packages | None | ⬜ |
| crates.io | Rust packages | None | ⬜ |
| StackExchange | Q&A sites | None (limited) | ⬜ |
| Dev.to | Developer articles | None | ⬜ |

---

## 🎬 MEDIA & ENTERTAINMENT

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| iTunes Search | Music, apps, podcasts | None | ⬜ |
| MusicBrainz | Music metadata | None | ⬜ |
| TVMaze | TV show data | None | ⬜ |
| OMDB | Movie/TV database | 🔑 Free | ⬜ |
| TMDB | Movies, TV shows | 🔑 Free | ⬜ |
| Podcast Index | Podcast search | 🔑 Free | ⬜ |
| Spotify | Music, playlists | 🔑 OAuth | ⬜ |
| YouTube Data | Videos, channels | 🔑 Free tier | ⬜ |

---

## 🎮 GAMES & FUN

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| PokéAPI | Pokemon data | None | ⬜ |
| SWAPI | Star Wars data | None | ⬜ |
| RAWG | Video game database | 🔑 Free | ⬜ |
| Deck of Cards | Card game API | None | ⬜ |
| Jikan | Anime/Manga (MyAnimeList) | None | ⬜ |
| Open Trivia DB | Quiz questions | None | ⬜ |
| xkcd | Comics | None | ⬜ |
| Cat Facts | Random cat facts | None | ⬜ |
| Dog API | Dog images | None | ⬜ |

---

## 💰 FINANCE & CRYPTO

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| ExchangeRate-API | Currency conversion | None (limited) | ⬜ |
| CoinGecko | Crypto prices | None | ⬜ |
| CoinCap | Crypto market data | None | ⬜ |
| SEC EDGAR | Company filings (US) | None | ⬜ |
| Yahoo Finance | Stock data (unofficial) | None ⚠️ | ⬜ |
| Alpha Vantage | Stocks, forex, crypto | 🔑 Free | ⬜ |

---

## 📰 NEWS & FEEDS

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| NewsAPI | News aggregation | 🔑 Free | ⬜ |
| GNews | Google News | 🔑 Free | ⬜ |
| Hacker News | Tech news | None | ✅ |
| Reddit (.json) | Subreddit feeds | None ⚠️ | ✅ |
| RSS Feeds | Any RSS source | None | ⬜ |

---

## 🛠️ UTILITIES

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| JSONPlaceholder | Fake REST API (testing) | None | ⬜ |
| httpbin | HTTP testing | None | ⬜ |
| QRCode Generator | Generate QR codes | None | ⬜ |
| UUID Generator | Generate UUIDs | None | ⬜ |
| Lorem Ipsum | Placeholder text | None | ⬜ |
| Country Flags | Flag images | None | ⬜ |

---

## 🌍 GEO & COUNTRY DATA

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| REST Countries | Country info, flags | None | ✅ |
| Country State City | Location hierarchies | None | ⬜ |
| IP Geolocation | Location from IP | None (limited) | ⬜ |
| Timezone DB | Timezone data | None | ⬜ |

---

## 🔬 SCIENCE & DATA

| API | Description | Auth | Status |
|-----|-------------|------|--------|
| NASA APOD | Astronomy picture of day | None | ⬜ |
| NASA Mars Rover | Mars photos | 🔑 Free | ⬜ |
| SpaceX | Launch data | None | ⬜ |
| USGS | Geological data | None | ⬜ |
| World Bank | Economic indicators | None | ⬜ |
| Data.gov | US government data | None | ⬜ |

---

## 📊 Completion Summary

| Category | Complete | Total | Progress |
|----------|----------|-------|----------|
| Reference & Knowledge | 1 | 8 | 12% |
| Search Engines | 4 | 5 | 80% |
| Maps & Location | 0 | 7 | 0% |
| Weather & Environment | 1 | 4 | 25% |
| Social Platforms | 2 | 8 | 25% |
| Developer Platforms | 1 | 7 | 14% |
| Media & Entertainment | 0 | 8 | 0% |
| Games & Fun | 0 | 9 | 0% |
| Finance & Crypto | 0 | 6 | 0% |
| News & Feeds | 2 | 5 | 40% |
| Utilities | 0 | 6 | 0% |
| Geo & Country Data | 1 | 4 | 25% |
| Science & Data | 0 | 6 | 0% |
| **TOTAL** | **12** | **83** | **14%** |

---

## Notes

**Truly Keyless**: No signup, no API key, just call the endpoint
- Wikipedia, Open-Meteo, REST Countries, Reddit JSON, Hacker News, DuckDuckGo, SearXNG

**Free Tier (Key Required)**: Free but requires registration
- Google APIs, Bing, YouTube, TMDB, NewsAPI, Alpha Vantage

Both are valuable - the key distinction is friction to get started.

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
