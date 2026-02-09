# Getting Started

Welcome to the Gibs API documentation. Gibs provides AI-powered EU regulatory compliance analysis via a simple REST API.

---

## Quick Start

### 1. Get an API Key

Sign up at [gibs.dev](https://gibs.dev) and create an API key in your dashboard.

### 2. Make Your First Request

```bash
curl -X POST https://api.gibs.dev/v1/classify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"description": "AI system that screens CVs for job applications"}'
```

### 3. Get Your Response

```json
{
  "risk_level": "high",
  "confidence": 0.7,
  "sources": [
    {"article": "Article 6", "title": "Classification rules for high-risk AI systems"},
    {"article": "Annex III", "title": "High-risk AI systems", "point": "4(a)"}
  ],
  "summary": "CV screening AI is classified as high-risk under Article 6(2) and Annex III, point 4(a) — AI systems intended to be used for recruitment or selection of natural persons.",
  "processing_time_ms": 4300
}
```

That's it. No SDK required, no dependencies. Any HTTP client works.

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

Additional regulations will be announced on our changelog.
