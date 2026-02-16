# API Endpoints

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
| `description` | string | Yes | Plain-language description of the AI system (max 2000 chars) |

### Response

```json
{
  "risk_level": "prohibited",
  "confidence": 0.9,
  "sources": [
    {
      "article": "Article 5(1)(f)",
      "title": "Prohibited AI practices",
      "text": "AI systems that infer emotions of a natural person in the areas of workplace..."
    },
    {
      "article": "Recital 44",
      "title": "Emotion recognition in workplace"
    }
  ],
  "summary": "Workplace emotion detection AI is prohibited under Article 5(1)(f) of the AI Act, with limited exceptions for medical or safety purposes.",
  "processing_time_ms": 4100
}
```

| Field | Type | Description |
|-------|------|-------------|
| `risk_level` | string | `prohibited`, `high`, `limited`, `minimal`, or `unknown` |
| `confidence` | float | 0.0–1.0 confidence in classification |
| `sources` | array | Cited regulatory articles with titles |
| `summary` | string | Brief explanation of classification |
| `processing_time_ms` | int | Server-side processing time |

---

## POST /v1/check

Ask a compliance question and receive a detailed, sourced answer.

**Use case**: In-depth regulatory Q&A. Returns full analysis with article citations and cross-references. Thorough response (~10s).

### Request

```bash
curl -X POST https://api.gibs.dev/v1/check \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "What are the obligations for deployers of high-risk AI systems used in HR?"
  }'
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `question` | string | Yes | Compliance question in natural language (max 5000 chars) |
| `regulation` | string | No | Target regulation: `ai_act`, `gdpr`, `dora`, or `auto` (default: `auto` — detected from question) |
| `system_context` | object | No | Optional context about your system (e.g. `{"sector": "healthcare"}`) |

### Response

```json
{
  "answer": "Deployers of high-risk AI systems in HR contexts have several obligations under the AI Act...",
  "confidence": "high",
  "sources": [
    {
      "article_id": "Article 26",
      "title": "Obligations of deployers of high-risk AI systems",
      "text_excerpt": "...",
      "relevance_score": 0.95
    },
    {
      "article_id": "Article 14",
      "title": "Human oversight",
      "text_excerpt": "...",
      "relevance_score": 0.88
    },
    {
      "article_id": "GDPR Article 22",
      "title": "Automated individual decision-making",
      "text_excerpt": "...",
      "relevance_score": 0.78
    }
  ],
  "should_abstain": false,
  "abstention_reason": null,
  "request_id": "a1b2c3d4-...",
  "corpus_version": "v1.1.0",
  "processing_time_ms": 9600
}
```

| Field | Type | Description |
|-------|------|-------------|
| `answer` | string | Detailed answer with inline article citations |
| `confidence` | string | `high`, `medium`, or `low` |
| `sources` | array | Cited articles with relevance scores |
| `should_abstain` | boolean | `true` if sources don't cover the question |
| `abstention_reason` | string\|null | Explanation if abstaining |
| `request_id` | string | Unique request identifier |
| `corpus_version` | string | Version of the legal corpus used |
| `processing_time_ms` | int | Server-side processing time |

### Completeness Score

The `completeness` field indicates how thoroughly the question was answered based on available regulatory text:

| Score | Meaning |
|-------|---------|
| 0.8–1.0 | Comprehensive answer with strong source coverage |
| 0.5–0.8 | Partial answer — some relevant provisions may not be covered |
| 0.0–0.5 | Limited coverage — question may fall outside indexed regulations |

When completeness is below 0.5, the response will explicitly state what could not be answered and why.

---

## GET /v1/health

Check API status.

```bash
curl https://api.gibs.dev/v1/health
```

```json
{
  "status": "healthy",
  "version": "0.1.0",
  "corpus_version": "2026-02-09",
  "regulations": ["ai_act", "gdpr", "dora"]
}
```

No authentication required.
