# API Endpoints

Base URL: `https://api.gibs.dev`

All requests require authentication via Bearer token in the Authorization header.

---

## POST /v1/classify

Classify an AI system's risk level under the EU AI Act.

### Request

```json
{
  "description": "string (required) - What the AI system does",
  "data_types": ["string"] ,
  "decision_scope": "string",
  "sector": "string",
  "jurisdiction": "string (default: EU)"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `description` | string | Yes | Description of what the AI system does (10-5000 chars) |
| `data_types` | string[] | No | Types of data processed (e.g., "biometric", "personal") |
| `decision_scope` | string | No | What decisions the system influences |
| `sector` | string | No | Industry sector (e.g., "healthcare", "finance", "hr") |
| `jurisdiction` | string | No | Target jurisdiction (default: "EU") |

### Response

```json
{
  "risk_level": "high",
  "confidence": 0.92,
  "reasoning": "string",
  "obligations": [
    {
      "id": "string",
      "title": "string",
      "article": "string"
    }
  ],
  "sources": [
    {
      "article_id": "string",
      "title": "string",
      "text_excerpt": "string",
      "relevance_score": 0.95
    }
  ],
  "request_id": "abc123",
  "corpus_version": "v0.1.0",
  "processing_time_ms": 4200
}
```

### Example

```bash
curl -X POST https://api.gibs.dev/v1/classify \
  -H "Authorization: Bearer sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "description": "Facial recognition for employee access control",
    "data_types": ["biometric"],
    "sector": "corporate"
  }'
```

---

## POST /v1/check

Ask any compliance question and get a grounded answer with source citations.

### Request

```json
{
  "question": "string (required)",
  "system_context": {
    "description": "string"
  },
  "regulation": "string (default: both)"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `question` | string | Yes | Your compliance question (10-2000 chars) |
| `system_context` | object | No | Context about your AI system |
| `regulation` | string | No | Focus area: "ai_act", "gdpr", or "both" |

### Response

```json
{
  "answer": "string with [Article X] citations",
  "confidence": "high",
  "sources": [
    {
      "article_id": "string",
      "title": "string",
      "text_excerpt": "string",
      "relevance_score": 0.95
    }
  ],
  "should_abstain": false,
  "abstention_reason": null,
  "request_id": "def456",
  "corpus_version": "v0.1.0",
  "processing_time_ms": 9500
}
```

### Example

```bash
curl -X POST https://api.gibs.dev/v1/check \
  -H "Authorization: Bearer sk_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What are the transparency requirements for chatbots under the EU AI Act?",
    "regulation": "ai_act"
  }'
```

---

## GET /health

Check API health status.

### Response

```json
{
  "status": "healthy",
  "version": "0.1.0",
  "corpus_version": "v0.1.0"
}
```

---

## Error Responses

All errors follow this format:

```json
{
  "detail": "Error message"
}
```

### Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 400 | Bad Request | Check your request format |
| 401 | Unauthorized | Check your API key |
| 403 | Forbidden | Your plan doesn't include this feature |
| 429 | Rate Limited | Wait and retry, or upgrade plan |
| 500 | Server Error | Retry, contact support with request_id |

---

## Rate Limits

| Plan | Requests/minute | Requests/month |
|------|-----------------|----------------|
| Free | 10 | 50 |
| Pro | 30 | 2,000 |
| Business | 60 | 10,000 |

Rate limit headers are included in every response:
- `X-RateLimit-Limit`: Your limit
- `X-RateLimit-Remaining`: Requests remaining
- `X-RateLimit-Reset`: Unix timestamp when limit resets
