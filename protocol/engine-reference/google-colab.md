# Google Colab Engine Reference

## Building a Schema Engine in Python Notebooks

Google Colab provides a powerful environment for API integration with built-in Google authentication and full Python capabilities.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     Google Colab                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Schema    │  │  google.    │  │  requests   │        │
│  │   YAML      │──▶│   auth      │──▶│             │        │
│  │             │  │             │  │             │        │
│  │  PyYAML     │  │ Credentials │  │ HTTP Client │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Built-in Google Authentication

Colab provides seamless Google API access:

```python
from google.colab import auth
auth.authenticate_user()

# Now you have access to Google APIs
import gspread
from google.auth import default

creds, _ = default()
gc = gspread.authorize(creds)
```

---

## Schema-Driven Engine

### Install Dependencies
```python
!pip install pyyaml requests google-auth google-auth-oauthlib
```

### Load Schema
```python
import yaml

SCHEMA_YAML = """
openapi: "3.1.0"
info:
  title: Google Calendar API
  version: "1.0.0"
servers:
  - url: https://www.googleapis.com/calendar/v3
paths:
  /calendars/{calendarId}/events:
    get:
      operationId: listCalendarEvents
      parameters:
        - name: calendarId
          in: path
          required: true
        - name: maxResults
          in: query
        - name: timeMin
          in: query
"""

schema = yaml.safe_load(SCHEMA_YAML)
```

### Engine Implementation
```python
import requests
from google.colab import auth
from google.auth import default
from google.auth.transport.requests import Request

class ColabSchemaEngine:
    def __init__(self, schema):
        self.schema = schema
        self.base_url = schema['servers'][0]['url']
        self.credentials = None
        
    def authenticate(self):
        """Trigger Google OAuth flow"""
        auth.authenticate_user()
        self.credentials, _ = default()
        
    def get_access_token(self):
        """Get valid access token, refreshing if needed"""
        if not self.credentials:
            self.authenticate()
        if self.credentials.expired:
            self.credentials.refresh(Request())
        return self.credentials.token
    
    def call(self, operation_id, **params):
        """Execute an operation by ID"""
        # Find operation in schema
        operation = self._find_operation(operation_id)
        if not operation:
            raise ValueError(f"Unknown operation: {operation_id}")
        
        method, path_template, op_def = operation
        
        # Build URL with path parameters
        url = self.base_url + path_template
        query_params = {}
        
        for param_def in op_def.get('parameters', []):
            param_name = param_def['name']
            param_in = param_def['in']
            
            if param_name in params:
                if param_in == 'path':
                    url = url.replace(f'{{{param_name}}}', str(params[param_name]))
                elif param_in == 'query':
                    query_params[param_name] = params[param_name]
        
        # Make request
        headers = {'Authorization': f'Bearer {self.get_access_token()}'}
        
        response = requests.request(
            method=method.upper(),
            url=url,
            params=query_params,
            headers=headers
        )
        
        response.raise_for_status()
        return response.json()
    
    def _find_operation(self, operation_id):
        """Find operation definition by operationId"""
        for path, methods in self.schema.get('paths', {}).items():
            for method, op_def in methods.items():
                if method in ['get', 'post', 'put', 'patch', 'delete']:
                    if op_def.get('operationId') == operation_id:
                        return (method, path, op_def)
        return None
```

### Usage
```python
# Initialize
engine = ColabSchemaEngine(schema)
engine.authenticate()

# Call operations by ID
events = engine.call('listCalendarEvents', 
    calendarId='primary',
    maxResults=10
)

for event in events.get('items', []):
    print(f"- {event.get('summary', 'No title')}")
```

---

## External OAuth2 (Non-Google)

For APIs outside Google ecosystem:

```python
from google_auth_oauthlib.flow import InstalledAppFlow

# Your OAuth credentials
CLIENT_CONFIG = {
    "installed": {
        "client_id": "{{CLIENT_ID}}",
        "client_secret": "{{CLIENT_SECRET}}",
        "auth_uri": "https://accounts.google.com/o/oauth2/auth",
        "token_uri": "https://oauth2.googleapis.com/token",
    }
}

SCOPES = ['https://www.googleapis.com/auth/calendar']

def get_oauth_credentials():
    flow = InstalledAppFlow.from_client_config(CLIENT_CONFIG, SCOPES)
    credentials = flow.run_local_server(port=0)
    return credentials

creds = get_oauth_credentials()
```

---

## API Key Engine (Simpler)

```python
class ApiKeyEngine:
    def __init__(self, schema, api_key, key_header='X-API-Key'):
        self.schema = schema
        self.api_key = api_key
        self.key_header = key_header
        self.base_url = schema['servers'][0]['url']
    
    def call(self, operation_id, **params):
        operation = self._find_operation(operation_id)
        method, path, op_def = operation
        
        url = self.base_url + path
        query_params = {}
        
        for param_def in op_def.get('parameters', []):
            param_name = param_def['name']
            if param_name in params:
                if param_def['in'] == 'path':
                    url = url.replace(f'{{{param_name}}}', str(params[param_name]))
                else:
                    query_params[param_name] = params[param_name]
        
        headers = {self.key_header: self.api_key}
        
        response = requests.request(
            method=method.upper(),
            url=url,
            params=query_params,
            headers=headers
        )
        
        return response.json()
```

---

## Keyless Engine (Simplest)

```python
class KeylessEngine:
    def __init__(self, schema):
        self.schema = schema
        self.base_url = schema['servers'][0]['url']
    
    def call(self, operation_id, **params):
        operation = self._find_operation(operation_id)
        method, path, op_def = operation
        
        url = self.base_url + path
        for param_def in op_def.get('parameters', []):
            param_name = param_def['name']
            if param_name in params and param_def['in'] == 'path':
                url = url.replace(f'{{{param_name}}}', str(params[param_name]))
        
        query_params = {k: v for k, v in params.items() 
                       if f'{{{k}}}' not in path}
        
        response = requests.get(url, params=query_params)
        return response.json()

# Usage
weather_schema = yaml.safe_load("""
openapi: "3.1.0"
servers:
  - url: https://api.open-meteo.com/v1
paths:
  /forecast:
    get:
      operationId: getWeather
      parameters:
        - name: latitude
          in: query
        - name: longitude
          in: query
        - name: current_weather
          in: query
""")

engine = KeylessEngine(weather_schema)
weather = engine.call('getWeather', 
    latitude=52.52, 
    longitude=13.41, 
    current_weather=True
)
print(weather['current_weather'])
```

---

## Credential Management

### Environment Variables
```python
import os
os.environ['API_KEY'] = 'sk-xxx'
api_key = os.environ.get('API_KEY')
```

### Colab Secrets (Recommended)
```python
from google.colab import userdata
api_key = userdata.get('API_KEY')
```

### File-based (Persistent)
```python
from google.colab import drive
drive.mount('/content/drive')

# Store credentials in Drive
with open('/content/drive/MyDrive/credentials.json', 'r') as f:
    creds = json.load(f)
```

---

## Key Advantages of Colab

1. **Free GPU/TPU**: For ML-enhanced API processing
2. **Pre-installed packages**: NumPy, Pandas, requests
3. **Google Auth built-in**: Seamless Google API access
4. **Shareable notebooks**: Collaborative development
5. **No local setup**: Browser-based execution

---

## Comparison with Other Engines

| Aspect | Colab | ChatGPT | Apps Script |
|--------|-------|---------|-------------|
| OAuth management | Manual/library | Automatic | OAuth2 library |
| Execution | Notebook cells | Automatic | Script/trigger |
| Data processing | Full Python | Limited | JavaScript |
| Persistence | Drive/runtime | None | Properties |

---

*Colab excels for data-heavy API work and experimentation.*
