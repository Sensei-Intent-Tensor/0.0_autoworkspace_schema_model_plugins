# Node.js Engine Reference

## Building a Schema Engine in Node.js

Node.js provides full control over OAuth flows and HTTP requests, making it ideal for production schema engines.

---

## Quick Start

```bash
npm init -y
npm install axios yaml dotenv
npm install googleapis  # For Google OAuth
```

---

## Schema-Driven Engine

```javascript
const axios = require('axios');
const yaml = require('yaml');
const fs = require('fs');

class SchemaEngine {
  constructor(schemaPath, config) {
    const schemaText = fs.readFileSync(schemaPath, 'utf8');
    this.schema = yaml.parse(schemaText);
    this.config = config;
    this.baseUrl = this.schema.servers[0].url;
    this.accessToken = null;
  }

  async call(operationId, params = {}, body = null) {
    const operation = this.findOperation(operationId);
    if (!operation) throw new Error(`Unknown operation: ${operationId}`);

    const { method, path, definition } = operation;
    
    // Build URL
    let url = this.baseUrl + path;
    const queryParams = {};
    
    for (const paramDef of definition.parameters || []) {
      const { name, in: location } = paramDef;
      if (params[name] !== undefined) {
        if (location === 'path') {
          url = url.replace(`{${name}}`, encodeURIComponent(params[name]));
        } else if (location === 'query') {
          queryParams[name] = params[name];
        }
      }
    }

    // Make request
    const response = await axios({
      method,
      url,
      params: queryParams,
      data: body,
      headers: await this.getAuthHeaders()
    });

    return response.data;
  }

  findOperation(operationId) {
    for (const [path, methods] of Object.entries(this.schema.paths || {})) {
      for (const [method, definition] of Object.entries(methods)) {
        if (definition.operationId === operationId) {
          return { method, path, definition };
        }
      }
    }
    return null;
  }

  async getAuthHeaders() {
    const security = this.schema.components?.securitySchemes;
    if (!security) return {};

    // Handle different auth types
    if (security.OAuth2) {
      return { Authorization: `Bearer ${await this.getAccessToken()}` };
    }
    if (security.ApiKeyAuth) {
      const { name, in: location } = security.ApiKeyAuth;
      if (location === 'header') {
        return { [name]: this.config.API_KEY };
      }
    }
    return {};
  }

  async getAccessToken() {
    // Implement OAuth2 token management
    if (this.accessToken) return this.accessToken;
    // ... token refresh logic
    return this.accessToken;
  }
}

module.exports = SchemaEngine;
```

---

## OAuth2 with Google APIs

```javascript
const { google } = require('googleapis');

const oauth2Client = new google.auth.OAuth2(
  process.env.CLIENT_ID,
  process.env.CLIENT_SECRET,
  process.env.CALLBACK_URL
);

// Generate auth URL
const authUrl = oauth2Client.generateAuthUrl({
  access_type: 'offline',
  scope: ['https://www.googleapis.com/auth/calendar']
});

// Exchange code for tokens
async function handleCallback(code) {
  const { tokens } = await oauth2Client.getToken(code);
  oauth2Client.setCredentials(tokens);
  return tokens;
}

// Use with schema engine
class GoogleSchemaEngine extends SchemaEngine {
  constructor(schemaPath, oauth2Client) {
    super(schemaPath, {});
    this.oauth2Client = oauth2Client;
  }

  async getAccessToken() {
    const { token } = await this.oauth2Client.getAccessToken();
    return token;
  }
}
```

---

## API Key Engine

```javascript
class ApiKeyEngine {
  constructor(schema, apiKey, keyHeader = 'X-API-Key') {
    this.schema = schema;
    this.apiKey = apiKey;
    this.keyHeader = keyHeader;
    this.baseUrl = schema.servers[0].url;
  }

  async call(operationId, params = {}) {
    const operation = this.findOperation(operationId);
    const { method, path } = operation;
    
    let url = this.baseUrl + path;
    const queryParams = {};
    
    for (const [key, value] of Object.entries(params)) {
      if (path.includes(`{${key}}`)) {
        url = url.replace(`{${key}}`, encodeURIComponent(value));
      } else {
        queryParams[key] = value;
      }
    }

    const response = await axios({
      method,
      url,
      params: queryParams,
      headers: { [this.keyHeader]: this.apiKey }
    });

    return response.data;
  }
}
```

---

## Express Server Example

```javascript
const express = require('express');
const SchemaEngine = require('./engine');

const app = express();
app.use(express.json());

const engine = new SchemaEngine('./schemas/calendar.yaml', {
  CLIENT_ID: process.env.CLIENT_ID,
  CLIENT_SECRET: process.env.CLIENT_SECRET
});

app.post('/api/execute', async (req, res) => {
  try {
    const { operation, params, body } = req.body;
    const result = await engine.call(operation, params, body);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.listen(3000, () => console.log('Schema engine running on :3000'));
```

---

## Environment Configuration

```bash
# .env
CLIENT_ID=your-client-id
CLIENT_SECRET=your-client-secret
CALLBACK_URL=http://localhost:3000/callback
API_KEY=your-api-key
```

```javascript
require('dotenv').config();

const config = {
  CLIENT_ID: process.env.CLIENT_ID,
  CLIENT_SECRET: process.env.CLIENT_SECRET,
  CALLBACK_URL: process.env.CALLBACK_URL,
  API_KEY: process.env.API_KEY
};
```

---

## Key Advantages

1. **Full ecosystem**: NPM packages for everything
2. **Production-ready**: Express, Fastify for servers
3. **Async/await**: Clean async code
4. **TypeScript support**: Type-safe schemas
5. **Deployment options**: Vercel, Railway, Render

---

*Node.js is ideal for production schema engines and web services.*
