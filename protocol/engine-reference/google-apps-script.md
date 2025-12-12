# Google Apps Script Engine Reference

## Building a Schema Engine in Apps Script

Google Apps Script (GAS) provides built-in OAuth2 handling for Google APIs and can be extended for external APIs. This document shows how to build a schema-compliant engine.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                   Google Apps Script                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Schema    │  │   OAuth2    │  │ UrlFetchApp │        │
│  │   Config    │──▶│   Library   │──▶│             │        │
│  │             │  │             │  │             │        │
│  │ JSON/Object │  │ Service     │  │ HTTP Client │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│         │                │                │                │
│         ▼                ▼                ▼                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │              PropertiesService                       │  │
│  │              (Token Storage)                         │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Built-in Google API Access

For Google APIs, Apps Script provides automatic OAuth:

```javascript
// No manual OAuth needed - uses script's permissions
function listCalendarEvents() {
  const calendarId = 'primary';
  const events = CalendarApp.getCalendarById(calendarId).getEvents(
    new Date(),
    new Date(Date.now() + 7 * 24 * 60 * 60 * 1000)
  );
  return events.map(e => ({
    title: e.getTitle(),
    start: e.getStartTime(),
    end: e.getEndTime()
  }));
}
```

But for schema portability, use the REST API approach:

```javascript
function listCalendarEventsREST() {
  const calendarId = 'primary';
  const url = `https://www.googleapis.com/calendar/v3/calendars/${calendarId}/events`;
  
  const response = UrlFetchApp.fetch(url, {
    headers: {
      'Authorization': 'Bearer ' + ScriptApp.getOAuthToken()
    }
  });
  
  return JSON.parse(response.getContentText());
}
```

---

## OAuth2 Library for External APIs

For non-Google OAuth2, use the OAuth2 library:

### Setup
1. Add library: `1B7FSrk5Zi6L1rSxxTDgDEUsPzlukDsi4KGuTMorsTQHhGBzBkMun4iDF`
2. Give it identifier `OAuth2`

### Implementation
```javascript
function getGoogleOAuth2Service() {
  return OAuth2.createService('google')
    .setAuthorizationBaseUrl('https://accounts.google.com/o/oauth2/v2/auth')
    .setTokenUrl('https://oauth2.googleapis.com/token')
    .setClientId(getConfig().CLIENT_ID)
    .setClientSecret(getConfig().CLIENT_SECRET)
    .setCallbackFunction('authCallback')
    .setPropertyStore(PropertiesService.getUserProperties())
    .setScope('https://www.googleapis.com/auth/calendar')
    .setParam('access_type', 'offline')
    .setParam('prompt', 'consent');
}

function authCallback(request) {
  const service = getGoogleOAuth2Service();
  const authorized = service.handleCallback(request);
  if (authorized) {
    return HtmlService.createHtmlOutput('Success! You can close this tab.');
  } else {
    return HtmlService.createHtmlOutput('Denied. Please try again.');
  }
}

function getAuthorizationUrl() {
  return getGoogleOAuth2Service().getAuthorizationUrl();
}
```

---

## Schema-Driven Engine

### Configuration Object (Mirrors Schema)
```javascript
const SCHEMA_CONFIG = {
  baseUrl: 'https://www.googleapis.com/calendar/v3',
  auth: {
    type: 'oauth2',
    authorizationUrl: 'https://accounts.google.com/o/oauth2/v2/auth',
    tokenUrl: 'https://oauth2.googleapis.com/token',
    scopes: ['https://www.googleapis.com/auth/calendar']
  },
  operations: {
    listCalendarEvents: {
      method: 'GET',
      path: '/calendars/{calendarId}/events',
      params: ['calendarId', 'timeMin', 'timeMax', 'maxResults']
    },
    createEvent: {
      method: 'POST',
      path: '/calendars/{calendarId}/events',
      params: ['calendarId'],
      body: true
    }
  }
};

// Placeholders - user fills these
const USER_CONFIG = {
  CLIENT_ID: '{{CLIENT_ID}}',
  CLIENT_SECRET: '{{CLIENT_SECRET}}'
};
```

### Engine Implementation
```javascript
class SchemaEngine {
  constructor(schema, userConfig) {
    this.schema = schema;
    this.config = userConfig;
    this.service = this.buildOAuthService();
  }
  
  buildOAuthService() {
    if (this.schema.auth.type !== 'oauth2') return null;
    
    return OAuth2.createService('schema_auth')
      .setAuthorizationBaseUrl(this.schema.auth.authorizationUrl)
      .setTokenUrl(this.schema.auth.tokenUrl)
      .setClientId(this.config.CLIENT_ID)
      .setClientSecret(this.config.CLIENT_SECRET)
      .setCallbackFunction('authCallback')
      .setPropertyStore(PropertiesService.getUserProperties())
      .setScope(this.schema.auth.scopes.join(' '))
      .setParam('access_type', 'offline');
  }
  
  call(operationId, params = {}, body = null) {
    const operation = this.schema.operations[operationId];
    if (!operation) throw new Error(`Unknown operation: ${operationId}`);
    
    // Build URL with path params
    let url = this.schema.baseUrl + operation.path;
    for (const [key, value] of Object.entries(params)) {
      url = url.replace(`{${key}}`, encodeURIComponent(value));
    }
    
    // Build query params
    const queryParams = {};
    for (const param of operation.params || []) {
      if (params[param] && !operation.path.includes(`{${param}}`)) {
        queryParams[param] = params[param];
      }
    }
    if (Object.keys(queryParams).length > 0) {
      url += '?' + Object.entries(queryParams)
        .map(([k, v]) => `${k}=${encodeURIComponent(v)}`)
        .join('&');
    }
    
    // Build request options
    const options = {
      method: operation.method,
      headers: this.getAuthHeaders(),
      muteHttpExceptions: true
    };
    
    if (body && operation.body) {
      options.contentType = 'application/json';
      options.payload = JSON.stringify(body);
    }
    
    // Execute
    const response = UrlFetchApp.fetch(url, options);
    const code = response.getResponseCode();
    
    if (code === 401) {
      // Token expired - refresh and retry
      this.service.refresh();
      options.headers = this.getAuthHeaders();
      return this.parseResponse(UrlFetchApp.fetch(url, options));
    }
    
    return this.parseResponse(response);
  }
  
  getAuthHeaders() {
    if (!this.service) return {};
    if (!this.service.hasAccess()) {
      throw new Error('Not authorized. Run getAuthorizationUrl() first.');
    }
    return {
      'Authorization': 'Bearer ' + this.service.getAccessToken()
    };
  }
  
  parseResponse(response) {
    const code = response.getResponseCode();
    const text = response.getContentText();
    
    if (code >= 200 && code < 300) {
      return JSON.parse(text);
    } else {
      throw new Error(`API Error ${code}: ${text}`);
    }
  }
}
```

### Usage
```javascript
// Initialize engine
const engine = new SchemaEngine(SCHEMA_CONFIG, USER_CONFIG);

// Call operations by ID (mirrors ChatGPT Actions)
function listMyEvents() {
  return engine.call('listCalendarEvents', {
    calendarId: 'primary',
    maxResults: 10
  });
}

function createNewEvent() {
  return engine.call('createEvent', 
    { calendarId: 'primary' },
    {
      summary: 'Meeting',
      start: { dateTime: '2025-01-15T10:00:00Z' },
      end: { dateTime: '2025-01-15T11:00:00Z' }
    }
  );
}
```

---

## API Key Engine (Simpler)

```javascript
class ApiKeyEngine {
  constructor(schema, apiKey) {
    this.schema = schema;
    this.apiKey = apiKey;
  }
  
  call(operationId, params = {}) {
    const operation = this.schema.operations[operationId];
    let url = this.schema.baseUrl + operation.path;
    
    // Add API key based on schema config
    const keyConfig = this.schema.auth;
    if (keyConfig.in === 'header') {
      // Will be added to headers
    } else if (keyConfig.in === 'query') {
      params[keyConfig.name] = this.apiKey;
    }
    
    // Build query string
    const queryString = Object.entries(params)
      .map(([k, v]) => `${k}=${encodeURIComponent(v)}`)
      .join('&');
    if (queryString) url += '?' + queryString;
    
    const options = {
      method: operation.method,
      headers: keyConfig.in === 'header' 
        ? { [keyConfig.name]: this.apiKey }
        : {},
      muteHttpExceptions: true
    };
    
    const response = UrlFetchApp.fetch(url, options);
    return JSON.parse(response.getContentText());
  }
}
```

---

## Credential Storage

### User Properties (Per User)
```javascript
// Store
PropertiesService.getUserProperties().setProperty('API_KEY', 'sk-xxx');

// Retrieve
const apiKey = PropertiesService.getUserProperties().getProperty('API_KEY');
```

### Script Properties (Global)
```javascript
// Store (for shared keys)
PropertiesService.getScriptProperties().setProperty('API_KEY', 'sk-xxx');

// Retrieve
const apiKey = PropertiesService.getScriptProperties().getProperty('API_KEY');
```

---

## Web App Deployment

To expose your engine as an HTTP endpoint:

```javascript
function doGet(e) {
  const operation = e.parameter.operation;
  const params = JSON.parse(e.parameter.params || '{}');
  
  const engine = new SchemaEngine(SCHEMA_CONFIG, getConfig());
  const result = engine.call(operation, params);
  
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  const data = JSON.parse(e.postData.contents);
  const { operation, params, body } = data;
  
  const engine = new SchemaEngine(SCHEMA_CONFIG, getConfig());
  const result = engine.call(operation, params, body);
  
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}
```

Deploy as web app to get an HTTPS endpoint.

---

## Key Differences from ChatGPT

| Aspect | ChatGPT Actions | Apps Script |
|--------|-----------------|-------------|
| OAuth management | Automatic | OAuth2 library |
| Token storage | Managed | PropertiesService |
| Credential entry | Builder UI | Script config |
| Deployment | Instant | Web app publish |
| User auth | Per GPT user | Per script user |

---

## Advantages of GAS

1. **Free hosting**: Google's infrastructure
2. **Native Google access**: Built-in Calendar/Drive/Sheets APIs
3. **Triggers**: Time-based automation
4. **Sheets integration**: Use spreadsheets as databases
5. **Web apps**: Expose as HTTP endpoints

---

*GAS is powerful for Google ecosystem automation with schema portability.*
