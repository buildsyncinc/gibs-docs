# Getting Started

Welcome to the Gibs API documentation. Gibs provides AI-powered EU regulatory compliance analysis via a simple REST API.

---

## Quick Start

### 1. Get an API Key

Sign up at [gibs.dev](https://gibs.dev) and create an API key in your dashboard.

### 2. Install an SDK (optional)

```bash
pip install gibs          # Python
npm install @gibs-dev/sdk # JavaScript/TypeScript
```

### 3. Make Your First Request

{% tabs %}
{% tab title="Python SDK" %}
```python
from gibs import GibsClient

client = GibsClient(api_key="YOUR_API_KEY")
result = client.classify("AI system that screens CVs for job applications")
print(result.risk_level)  # "high"
```
{% endtab %}
{% tab title="cURL" %}
```bash
curl -X POST https://api.gibs.dev/v1/classify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"description": "AI system that screens CVs for job applications"}'
```
{% endtab %}
{% endtabs %}

### 4. Get Your Response

```json
{
  "risk_level": "high",
  "confidence": 0.9,
  "reasoning": "This system is classified as high-risk under Annex III, Area 4(a)...",
  "sources": [
    {"article_id": "Article 6", "title": "Classification rules for high-risk AI systems", "relevance_score": 0.95},
    {"article_id": "Annex III", "title": "High-risk AI systems — Area 4: Employment", "relevance_score": 0.91}
  ],
  "request_id": "a1b2c3d4-...",
  "corpus_version": "v1.1.0",
  "processing_time_ms": 5200
}
```

SDKs are optional — any HTTP client works.

---

## Base URL

```
https://api.gibs.dev
```

## Authentication

All requests require a Bearer token in the Authorization header:

```
Authorization: Bearer gbs_live_abc123...
```

API keys start with `gbs_live_` (production) or `gbs_test_` (sandbox).

## Rate Limits

| Plan | Requests/day | Requests/minute |
|------|-------------|-----------------|
| Free | 50 | 5 |
| Starter (€49/mo) | 500 | 30 |
| Pro (€499/mo) | 5,000 | 100 |
| Enterprise | Custom | Custom |

Rate limit headers are included in every response:

```
X-RateLimit-Limit: 500
X-RateLimit-Remaining: 487
X-RateLimit-Reset: 1707523200
```

When rate limited, the API returns `429 Too Many Requests`.

## Error Handling

All errors follow a consistent format:

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Daily request limit exceeded. Upgrade your plan at gibs.dev.",
    "status": 429
  }
}
```

| Status | Code | Meaning |
|--------|------|---------|
| 400 | `invalid_request` | Missing or malformed parameters |
| 401 | `unauthorized` | Invalid or missing API key |
| 429 | `rate_limit_exceeded` | Too many requests |
| 500 | `internal_error` | Server error (retry with backoff) |
| 503 | `service_unavailable` | Temporary downtime |

## Supported Regulations

| Regulation | Coverage | Status |
|------------|----------|--------|
| EU AI Act (2024/1689) | Full text — 113 articles, 13 annexes, 180 recitals | ✅ Live |
| GDPR (2016/679) | Full text — 99 articles, 173 recitals | ✅ Live |
| DORA (2022/2554) | 64 articles + 12 delegated acts, 641 chunks | ✅ Live |

The API auto-detects which regulation a question targets. You can also specify explicitly via the `regulation` parameter.

Additional regulations will be announced on our [changelog](changelog.md).
