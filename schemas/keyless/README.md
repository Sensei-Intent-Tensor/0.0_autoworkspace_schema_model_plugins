# Keyless (Public) API Schemas

Drop-in schemas for APIs that require **no authentication**.

## Available Schemas

| Schema | API | Operations | Status |
|--------|-----|------------|--------|
| [open-meteo.yaml](weather/open-meteo.yaml) | Open-Meteo | Weather, Forecast, Air Quality | ✅ Complete |
| [rest-countries.yaml](rest-countries.yaml) | REST Countries | Country data, Search, Regions | ✅ Complete |

## Coming Soon

| Schema | API | Operations |
|--------|-----|------------|
| `wikipedia.yaml` | Wikipedia | Article search, Summaries |
| `github-public.yaml` | GitHub (Public) | Repos, Users, Gists |

---

## Quick Start: REST Countries

**No API key needed!**

```bash
# Get all countries
curl "https://restcountries.com/v3.1/all"

# Search by name
curl "https://restcountries.com/v3.1/name/germany"

# Get by code
curl "https://restcountries.com/v3.1/alpha/US"

# Get by region
curl "https://restcountries.com/v3.1/region/europe"

# Filter fields (reduce response size)
curl "https://restcountries.com/v3.1/all?fields=name,capital,population,flags"
```

### Available Endpoints

| Endpoint | Description | Example |
|----------|-------------|---------|
| `/all` | All 250+ countries | `/all` |
| `/name/{name}` | Search by name | `/name/united` |
| `/alpha/{code}` | Get by ISO code | `/alpha/US` or `/alpha/USA` |
| `/alpha?codes=` | Multiple codes | `/alpha?codes=US,GB,DE` |
| `/currency/{currency}` | By currency | `/currency/EUR` |
| `/lang/{language}` | By language | `/lang/spanish` |
| `/capital/{capital}` | By capital city | `/capital/berlin` |
| `/region/{region}` | By region | `/region/europe` |
| `/subregion/{subregion}` | By subregion | `/subregion/northern%20europe` |
| `/demonym/{demonym}` | By demonym | `/demonym/american` |
| `/translation/{name}` | By translated name | `/translation/deutschland` |

### Field Filtering

Reduce response size by specifying fields:

```
?fields=name,capital,population,flags,currencies,languages
```

Available fields: `name`, `tld`, `cca2`, `ccn3`, `cca3`, `cioc`, `independent`, `status`, `unMember`, `currencies`, `idd`, `capital`, `altSpellings`, `region`, `subregion`, `languages`, `translations`, `latlng`, `landlocked`, `borders`, `area`, `demonyms`, `flag`, `maps`, `population`, `gini`, `fifa`, `car`, `timezones`, `continents`, `flags`, `coatOfArms`, `startOfWeek`, `capitalInfo`, `postalCode`

---

## Quick Start: Open-Meteo

**No API key needed!**

```bash
# Current weather + 7-day forecast
curl "https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current_weather=true&daily=temperature_2m_max,temperature_2m_min"

# Geocoding (find coordinates)
curl "https://geocoding-api.open-meteo.com/v1/search?name=Berlin"
```

---

## Why Keyless APIs?

These APIs are perfect for:

- **Prototyping** - No signup friction
- **Education** - Students can start immediately  
- **Public data** - Weather, geography, reference data
- **ChatGPT Actions** - Simplest integration path

### Limitations

- Often rate-limited (be respectful)
- May lack features of paid APIs
- No SLA guarantees
- Data freshness varies

---

## ChatGPT Actions Setup

For keyless APIs in ChatGPT Actions:

1. Select **"None"** for authentication
2. Paste the schema YAML
3. Test immediately - no credentials needed!

---

*Part of the [Auto-Workspace-AI Schema Library](https://github.com/Sensei-Intent-Tensor/0.0_autoworkspace_schema_model_plugins)*
