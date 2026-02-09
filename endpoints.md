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
| `question` | string | Yes | Compliance question in natural language (max 2000 chars) |
| `regulation` | string | No | Filter to specific regulation: `ai_act`, `gdpr`, or `all` (default: `all`) |

### Response

```json
{
  "answer": "Deployers of high-risk AI systems in HR contexts have several obligations under the AI Act...",
  "sources": [
    {
      "article": "Article 26",
      "title": "Obligations of deployers of high-risk AI systems",
      "relevance": 0.95
    },
    {
      "article": "Article 14",
      "title": "Human oversight",
      "relevance": 0.88
    },
    {
      "article": "Article 9",
      "title": "Risk management system",
      "relevance": 0.82
    },
    {
      "article": "GDPR Article 22",
      "title": "Automated individual decision-making",
      "relevance": 0.78
    }
  ],
  "completeness": 0.85,
  "processing_time_ms": 9600
}
```

| Field | Type | Description |
|-------|------|-------------|
| `answer` | string | Detailed answer with inline citations |
| `sources` | array | All cited articles, ranked by relevance |
| `completeness` | float | 0.0–1.0 estimated completeness of the answer |
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
  "regulations": ["ai_act", "gdpr"]
}
```

No authentication required.
