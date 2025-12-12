# 0.0 AutoWorkspace Schema Model Plugins

## The Universal API Schema Library

**Get the powers without the auth trouble.**

This repository separates **what APIs do** (schemas) from **how you authenticate to them** (engines). Drop a schema into any compliant engine—ChatGPT Actions, Google Apps Script, Colab, Node, Python—and execute.

---

## 🎯 The Two Halves

### Half 1: Protocol & Engine Reference (`/protocol/`)
Documentation showing HOW authentication machinery works. This is what ChatGPT built when they created Actions—they solved OAuth handshaking, token refresh, CORS, redirect URIs. **Once.**

You don't rebuild engines. You learn how they work, then drop schemas into them.

### Half 2: Portable Schemas (`/schemas/`)
Pure API definitions with `{{PLACEHOLDER}}` variables. No dependencies. No `pip install`. No package negotiation. Just intent.

```yaml
# Example: Any schema works anywhere
authorization_url: https://accounts.google.com/o/oauth2/v2/auth
token_url: https://oauth2.googleapis.com/token
client_id: {{CLIENT_ID}}
client_secret: {{CLIENT_SECRET}}
callback_url: {{CALLBACK_URL}}
```

Swap your credentials. Execute anywhere.

---

## 📁 Repository Structure

```
/0.0_autoworkspace_schema_model_plugins/
│
├── README.md                              # You are here
├── SCHEMA_SPEC.md                         # The {{PLACEHOLDER}} standard
│
├── /protocol/                             # HALF 1: Reference documentation
│   ├── HANDSHAKE_SPEC.md                  # Universal protocol definition
│   ├── /auth-gates/                       # Auth pattern documentation
│   │   ├── oauth2.md
│   │   ├── apikey.md
│   │   └── keyless.md
│   └── /engine-reference/                 # HOW to build engines
│       ├── chatgpt-actions.md
│       ├── google-apps-script.md
│       ├── google-colab.md
│       ├── node.md
│       └── python.md
│
├── /schemas/                              # HALF 2: Drop-in schemas
│   ├── /oauth2/                           # Schemas requiring OAuth2
│   │   ├── /google/
│   │   │   ├── calendar.yaml
│   │   │   ├── gmail.yaml
│   │   │   ├── drive.yaml
│   │   │   ├── docs.yaml
│   │   │   ├── sheets.yaml
│   │   │   ├── contacts.yaml
│   │   │   └── tasks.yaml
│   │   ├── /microsoft/
│   │   └── /other/
│   ├── /apikey/                           # Schemas requiring API key
│   │   ├── /ai-services/
│   │   ├── /data-providers/
│   │   └── /utilities/
│   └── /keyless/                          # No auth required
│       ├── /weather/
│       ├── /reference/
│       └── /public-data/
│
├── /examples/                             # Full working examples
│   ├── /chatgpt-calendar-oauth2/
│   ├── /gas-gmail-oauth2/
│   └── /colab-weather-keyless/
│
└── /docs/
    ├── placeholder-variables.md
    └── render-picker-integration.md
```

---

## 🚀 Quick Start

### 1. Find Your Schema
Browse `/schemas/` by auth type → platform → service.

### 2. Copy the Schema
Each `.yaml` file is a complete OpenAPI 3.1.0 specification.

### 3. Replace Placeholders
```yaml
{{CLIENT_ID}}        → Your OAuth client ID
{{CLIENT_SECRET}}    → Your OAuth client secret
{{CALLBACK_URL}}     → Your platform's callback URL
{{API_KEY}}          → Your API key
{{SCOPE}}            → Required OAuth scope
```

### 4. Drop Into Your Engine
- **ChatGPT Actions**: Paste into the Actions builder
- **Apps Script**: Use with UrlFetchApp
- **Colab**: Use with requests library
- **Node/Python**: Use with any HTTP client

---

## 🔐 Auth Types Explained

| Auth Type | When to Use | ChatGPT Compatible |
|-----------|-------------|-------------------|
| **OAuth2** | User data access (Google, Microsoft) | ✅ Yes |
| **API Key** | Service access (OpenAI, weather APIs) | ✅ Yes |
| **Keyless** | Public data (no auth needed) | ✅ Yes |
| **Service Account** | Server-to-server | ❌ Use GAS/Colab |

---

## 🧬 Philosophy: Intent Tensor Theory

This library implements the **Intent Tensor** principle:

> Schemas are **permissioned collapse**—execution occurs when intent (your API call) aligns with state (valid authentication).

The auth gate is not friction. It's the permission model that enables powerful operations. This library makes those gates transparent and passage through them frictionless.

**Parent Theory**: [Intent Tensor Theory Institute](https://github.com/intent-tensor-theory)

**Handshake Protocol**: [Universal Protocol OS Handshake](https://github.com/intent-tensor-theory/0.0_git_universal_protocol_os_handshake)

---

## 🏢 Organization

**Auto-Workspace-AI** | [auto-workspace-ai.com](https://auto-workspace-ai.com)

Derived from Intent Tensor Theory. Applying recursive math frameworks to business automation.

---

## 📜 License

MIT License - Use freely, build anything.

---

*"The schema is the intent. The engine is the permission. Execution is the collapse."*
