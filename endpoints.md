# API Endpoints

OpenAPI 3.1 spec: [`api.gibs.dev/openapi.json`](https://api.gibs.dev/openapi.json)

---

## POST /v1/classify

Classify an AI system's risk level under the EU AI Act.

**Use case**: Quick risk assessment. Returns risk level, confidence score, and relevant articles. Fast response (~4s).

### Request

```bash
curl -X POST https://api.gibs.dev/v1/classify \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "description": "AI system that detects emotions of employees in the workplace"
  }'
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `description` | string | Yes | Plain-language description of the AI system (10-5000 chars) |
| `data_types` | string[] | No | Types of data processed (e.g. `["biometric", "health"]`) |
| `decision_scope` | string | No | What decisions the system makes or influences |
| `sector` | string | No | Industry sector (e.g. `"healthcare"`, `"finance"`) |

### Response

```json
{
  "risk_level": "prohibited",
  "confidence": 0.9,
  "reasoning": "Workplace emotion detection AI is prohibited under Article 5(1)(f)...",
  "obligations": [
    {
      "id": "no_deploy",
      "description": "This AI practice is prohibited and cannot be deployed in the EU",
      "source_article": "Article 5",
      "deadline": null
    }
  ],
  "sources": [
    {
      "article_id": "Article 5",
      "title": "Prohibited AI practices",
      "text_excerpt": "AI systems that infer emotions of a natural person in the areas of workplace...",
      "relevance_score": 0.95
    }
  ],
  "request_id": "a1b2c3d4-...",
  "corpus_version": "v0.1.0",
  "processing_time_ms": 4100
}
```

| Field | Type | Description |
|-------|------|-------------|
| `risk_level` | string | `prohibited`, `high`, `limited`, or `minimal` |
| `confidence` | float | 0.0-1.0 confidence in classification |
| `reasoning` | string | Explanation of why this risk level applies |
| `obligations` | array | Required compliance obligations for this risk level |
| `sources` | array | Cited articles with relevance scores |
| `request_id` | string | Unique request identifier |
| `corpus_version` | string | Version of the legal corpus used |
| `processing_time_ms` | int | Server-side processing time |

---

## POST /v1/check

Ask a compliance question and receive a detailed, sourced answer.

**Use case**: In-depth regulatory Q&A. Returns full analysis with article citations and cross-references. Thorough response (~10-30s).

### Request

```bash
curl -X POST https://api.gibs.dev/v1/check \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What are the obligations for deployers of high-risk AI systems used in HR?",
    "response_format": "structured"
  }'
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `question` | string | Yes | Compliance question in natural language (10-2000 chars) |
| `regulation` | string | No | Target regulation: `ai_act`, `gdpr`, `dora`, or `auto` (default: `auto`) |
| `system_context` | object | No | Optional context about your system (max 10 keys, 5KB) |
| `response_format` | string | No | `text` (default markdown) or `structured` (parsed JSON sections) |

### Response

```json
{
  "answer": "Deployers of high-risk AI systems in HR contexts have several obligations...",
  "structured": {
    "summary": "Deployers must implement human oversight, conduct impact assessments, and ensure transparency.",
    "legal_basis": "[Article 26(1)] requires deployers to use systems in accordance with instructions...",
    "requirements": [
      "Implement human oversight measures per Article 14",
      "Conduct fundamental rights impact assessment per Article 27",
      "Inform workers' representatives before deployment per Article 26(7)"
    ],
    "timeline": [
      "Obligations apply from 2 August 2026 for Annex III systems"
    ],
    "articles_cited": ["Article 26", "Article 14", "Article 27"]
  },
  "confidence": "high",
  "confidence_score": 0.89,
  "sources": [
    {
      "article_id": "Article 26",
      "title": "Obligations of deployers of high-risk AI systems",
      "text_excerpt": "...",
      "relevance_score": 0.95
    }
  ],
  "should_abstain": false,
  "abstention_reason": null,
  "request_id": "a1b2c3d4-...",
  "corpus_version": "v0.1.0",
  "processing_time_ms": 12400
}
```

| Field | Type | Description |
|-------|------|-------------|
| `answer` | string | Detailed answer with inline `[Article X]` citations (always present) |
| `structured` | object\|null | Parsed sections (only when `response_format: "structured"`) |
| `confidence` | string | `high`, `medium`, or `low` |
| `confidence_score` | float | Numeric confidence 0.0-1.0 for programmatic use |
| `sources` | array | Cited articles with relevance scores |
| `should_abstain` | boolean | `true` if sources don't cover the question |
| `abstention_reason` | string\|null | Explanation if abstaining |
| `request_id` | string | Unique request identifier |
| `corpus_version` | string | Version of the legal corpus used |
| `processing_time_ms` | int | Server-side processing time |

### Structured Answer Fields

When `response_format: "structured"`, the `structured` object contains:

| Field | Type | Description |
|-------|------|-------------|
| `summary` | string | Direct 1-2 sentence answer |
| `legal_basis` | string | Legal basis section with article references |
| `requirements` | string[] | Key compliance requirements as bullet points |
| `timeline` | string[] | Relevant dates and deadlines |
| `articles_cited` | string[] | All articles referenced in the answer |

### Confidence Score

The `confidence_score` (0-1) is computed from retrieval quality, citation density, and hedging signals. Use it for programmatic decisions:

| Score | Interpretation |
|-------|---------------|
| 0.8-1.0 | High confidence — well-sourced, specific answer |
| 0.5-0.8 | Medium confidence — partial sources or hedged language |
| 0.0-0.5 | Low confidence — limited sources, consider verifying |

---

## POST /v1/check/stream

Stream a compliance answer via Server-Sent Events. Same input as `/v1/check`, but streams the synthesis token by token.

**Use case**: Real-time UI. First token appears in ~5s instead of waiting ~20s for the full response.

### Request

Same parameters as `POST /v1/check`.

### Event Types

| Event | Data | Description |
|-------|------|-------------|
| `status` | `{"stage": "retrieving"}` | Pipeline stage change |
| `chunk` | `"text token"` | Synthesis text token |
| `complete` | `{sources, confidence, ...}` | Final metadata after synthesis |
| `done` | `{request_id, processing_time_ms}` | Request complete |
| `error` | `"error message"` | Error occurred |

---

## GET /v1/health

Check API status. No authentication required.

```bash
curl https://api.gibs.dev/v1/health
```

```json
{
  "status": "healthy",
  "environment": "production",
  "corpus_version": "v0.1.0",
  "components": {
    "api": "healthy",
    "qdrant": "healthy",
    "postgres": "healthy",
    "redis": "healthy"
  }
}
```

---

## API Key Management

### GET /v1/account/keys

List all active API keys for the authenticated organization. Returns metadata only — never the full key value.

### POST /v1/account/keys

Create a new API key. Maximum 5 active keys per organization. The full key is returned once — store it securely.

### DELETE /v1/account/keys/{key_id}

Revoke a specific API key. Cannot revoke the key used for the current request.
