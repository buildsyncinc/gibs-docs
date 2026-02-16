# Changelog

All notable changes to the Gibs API.

---

## [Unreleased]

### Coming Soon
- NIS2 regulation support
- Additional provider evaluation benchmarks

---

## [0.5.0] - 2026-02-16

### Added
- **DORA regulation support** — Digital Operational Resilience Act: 64 articles + 12 delegated/implementing acts, 641 indexed chunks. Covers ICT risk management, incident reporting, TLPT, third-party oversight
- **Streaming responses** — `POST /v1/check/stream` for real-time synthesis via Server-Sent Events
- **MCP server listed** on MCP directories for AI agent discovery

### Improved
- Question routing — expanded keyword detection for DORA-specific terms (ICT risk, TLPT, financial entities)
- Evaluation infrastructure — retry on rate limits, configurable delay between requests
- 90.8% accuracy on 30-question DORA benchmark (96.7% abstention accuracy)

### Corpus
- AI Act: 836 chunks (113 articles, 13 annexes, 180 recitals)
- GDPR: 276 chunks (99 articles, 173 recitals)
- DORA: 641 chunks (64 articles + 12 delegated acts)
- Total: 1,753 indexed legal chunks across 3 regulations

---

## [0.4.0] - 2026-02-15

### Added
- Python SDK (`pip install gibs`) — sync + async clients, full type coverage
- JavaScript SDK (`npm install @gibs-dev/sdk`) — TypeScript, native fetch, ESM + CJS
- Multi-regulation routing — questions automatically routed to the correct corpus

### Improved
- Source citations now include relevance scores from reranker
- Synthesis engine upgraded with stronger scope discipline

---

## [0.3.3] - 2026-02-13

### Added
- API key management — create, list, and revoke named keys from the dashboard
- Multiple keys per organization (up to 5) with per-key usage tracking
- Self-service key lifecycle: create for different environments, revoke individually

### Fixed
- Dashboard stability improvements
- Infrastructure health check reliability

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
