# Changelog

All notable changes to the Gibs API.

---

## [Unreleased]

### Coming Soon
- Python SDK (`pip install gibs`)
- JavaScript SDK (`npm install @gibs/client`)
- DORA regulation support (64 articles + 12 delegated acts)
- Webhook notifications for corpus updates

---

## [0.3.2] - 2026-02-12

### Added
- Corpus isolation — each regulation now has its own dedicated index for better precision
- Complete legal metadata on every source: document type, legal status, application date, binding status
- MCP integration section on landing page

### Fixed
- GDPR application date corrected
- Improved multi-corpus query routing

### Corpus
- AI Act: 836 chunks (113 articles, 13 annexes, 180 recitals)
- GDPR: 276 chunks
- Full metadata enrichment on all chunks

---

## [0.3.1] - 2026-02-12

### Improved
- Inference caching — faster responses on repeated queries
- Scoring accuracy — better boundary matching and negation handling

### Added
- Full purchase lifecycle: signup, checkout, upgrade, API key management, downgrade
- Upgraded synthesis engine for higher accuracy
- Abstention guard — smarter handling when no relevant sources exist

### Fixed
- Subscription cancellation properly clears payment state
- Webhook handlers resilient to edge cases

---

## [0.3.0] - 2026-02-10

### Added
- 90-question expert-curated evaluation benchmark
- Cross-reference resolution across 3600+ legal references
- Multi-pass verification pipeline with completeness checks
- API key authentication with per-organization rate limiting
- Stripe integration for subscription management

### Performance
- 88% accuracy on regulatory benchmark
- 100% abstention accuracy on out-of-scope questions
- Article-level citation in every response

---

## [0.1.0] - 2026-02-10

### Added
- Initial API release
- `POST /v1/classify` — AI system risk classification
- `POST /v1/check` — Compliance Q&A with citations
- `GET /health` — Health check endpoint
- EU AI Act corpus (113 articles, 13 annexes)
- GDPR selected articles
- Commission guidelines integration
- MCP server for AI agent integration

---

## Version Format

We use [Semantic Versioning](https://semver.org/):
- **MAJOR**: Breaking API changes
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, corpus updates

Corpus versions are tracked separately and included in API responses.
