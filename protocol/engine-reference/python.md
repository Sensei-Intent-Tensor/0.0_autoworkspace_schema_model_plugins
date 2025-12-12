# Python Engine Reference

## Building a Schema Engine in Python

Python offers excellent libraries for API integration, making it ideal for data processing pipelines and backend services.

---

## Quick Start

```bash
pip install requests pyyaml python-dotenv
pip install google-auth google-auth-oauthlib  # For Google OAuth
```

---

## Schema-Driven Engine

```python
import yaml
import requests
from typing import Dict, Any, Optional
from dataclasses import dataclass

@dataclass
class Operation:
    method: str
    path: str
    parameters: list

class SchemaEngine:
    def __init__(self, schema_path: str, config: Dict[str, str]):
        with open(schema_path, 'r') as f:
            self.schema = yaml.safe_load(f)
        self.config = config
        self.base_url = self.schema['servers'][0]['url']
        self.access_token = None
    
    def call(self, operation_id: str, params: Dict[str, Any] = None, 
             body: Dict[str, Any] = None) -> Dict[str, Any]:
        params = params or {}
        operation = self._find_operation(operation_id)
        
        if not operation:
            raise ValueError(f"Unknown operation: {operation_id}")
        
        # Build URL
        url = self.base_url + operation.path
        query_params = {}
        
        for param_def in operation.parameters:
            name = param_def['name']
            location = param_def['in']
            
            if name in params:
                if location == 'path':
                    url = url.replace(f'{{{name}}}', str(params[name]))
                elif location == 'query':
                    query_params[name] = params[name]
        
        # Make request
        headers = self._get_auth_headers()
        
        response = requests.request(
            method=operation.method.upper(),
            url=url,
            params=query_params,
            json=body,
            headers=headers
        )
        
        response.raise_for_status()
        return response.json()
    
    def _find_operation(self, operation_id: str) -> Optional[Operation]:
        for path, methods in self.schema.get('paths', {}).items():
            for method, definition in methods.items():
                if method in ['get', 'post', 'put', 'patch', 'delete']:
                    if definition.get('operationId') == operation_id:
                        return Operation(
                            method=method,
                            path=path,
                            parameters=definition.get('parameters', [])
                        )
        return None
    
    def _get_auth_headers(self) -> Dict[str, str]:
        security = self.schema.get('components', {}).get('securitySchemes', {})
        
        if 'OAuth2' in security:
            return {'Authorization': f'Bearer {self._get_access_token()}'}
        
        if 'ApiKeyAuth' in security:
            key_def = security['ApiKeyAuth']
            if key_def['in'] == 'header':
                return {key_def['name']: self.config.get('API_KEY', '')}
        
        return {}
    
    def _get_access_token(self) -> str:
        # Implement token management
        return self.access_token or ''
```

---

## OAuth2 with Google

```python
from google.oauth2.credentials import Credentials
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request
import os
import pickle

SCOPES = ['https://www.googleapis.com/auth/calendar']

def get_google_credentials():
    creds = None
    
    # Load cached credentials
    if os.path.exists('token.pickle'):
        with open('token.pickle', 'rb') as token:
            creds = pickle.load(token)
    
    # Refresh or get new credentials
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                'credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        
        # Save credentials
        with open('token.pickle', 'wb') as token:
            pickle.dump(creds, token)
    
    return creds

class GoogleSchemaEngine(SchemaEngine):
    def __init__(self, schema_path: str):
        super().__init__(schema_path, {})
        self.credentials = get_google_credentials()
    
    def _get_access_token(self) -> str:
        if self.credentials.expired:
            self.credentials.refresh(Request())
        return self.credentials.token
```

---

## API Key Engine

```python
class ApiKeyEngine:
    def __init__(self, schema: dict, api_key: str, key_header: str = 'X-API-Key'):
        self.schema = schema
        self.api_key = api_key
        self.key_header = key_header
        self.base_url = schema['servers'][0]['url']
    
    def call(self, operation_id: str, **params) -> dict:
        operation = self._find_operation(operation_id)
        
        url = self.base_url + operation.path
        query_params = {}
        
        for key, value in params.items():
            if f'{{{key}}}' in operation.path:
                url = url.replace(f'{{{key}}}', str(value))
            else:
                query_params[key] = value
        
        response = requests.request(
            method=operation.method.upper(),
            url=url,
            params=query_params,
            headers={self.key_header: self.api_key}
        )
        
        return response.json()
```

---

## Keyless Engine

```python
class KeylessEngine:
    def __init__(self, schema: dict):
        self.schema = schema
        self.base_url = schema['servers'][0]['url']
    
    def call(self, operation_id: str, **params) -> dict:
        operation = self._find_operation(operation_id)
        
        url = self.base_url + operation.path
        query_params = {}
        
        for key, value in params.items():
            if f'{{{key}}}' in operation.path:
                url = url.replace(f'{{{key}}}', str(value))
            else:
                query_params[key] = value
        
        response = requests.get(url, params=query_params)
        return response.json()

# Usage example
weather_schema = {
    'servers': [{'url': 'https://api.open-meteo.com/v1'}],
    'paths': {
        '/forecast': {
            'get': {
                'operationId': 'getWeather',
                'parameters': [
                    {'name': 'latitude', 'in': 'query'},
                    {'name': 'longitude', 'in': 'query'},
                ]
            }
        }
    }
}

engine = KeylessEngine(weather_schema)
weather = engine.call('getWeather', latitude=52.52, longitude=13.41)
```

---

## FastAPI Server

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional, Dict, Any

app = FastAPI()

engine = SchemaEngine('schemas/calendar.yaml', {
    'CLIENT_ID': os.environ['CLIENT_ID'],
    'CLIENT_SECRET': os.environ['CLIENT_SECRET']
})

class ExecuteRequest(BaseModel):
    operation: str
    params: Optional[Dict[str, Any]] = {}
    body: Optional[Dict[str, Any]] = None

@app.post("/api/execute")
async def execute(request: ExecuteRequest):
    try:
        result = engine.call(request.operation, request.params, request.body)
        return result
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

---

## Environment Configuration

```python
# .env
CLIENT_ID=your-client-id
CLIENT_SECRET=your-client-secret
API_KEY=your-api-key

# Python
from dotenv import load_dotenv
import os

load_dotenv()

config = {
    'CLIENT_ID': os.getenv('CLIENT_ID'),
    'CLIENT_SECRET': os.getenv('CLIENT_SECRET'),
    'API_KEY': os.getenv('API_KEY')
}
```

---

## Key Advantages

1. **Rich ecosystem**: requests, pandas, numpy
2. **Data processing**: Excellent for ETL pipelines
3. **Type hints**: Clean, documented code
4. **FastAPI**: Modern async web framework
5. **Deployment**: Docker, Lambda, Cloud Functions

---

*Python excels for data-intensive API workflows and ML integration.*
