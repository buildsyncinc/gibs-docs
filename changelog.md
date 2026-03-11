# Changelog

All notable changes to the Gibs API.

---

## [0.5.5] - 2026-03-11

### Added
- **Monthly usage quotas** — each plan now enforces a monthly request limit (Free: 25, Developer: 500, Team: 5,000, Platform: 50,000). Returns 429 with `X-Quota-Limit` and `X-Quota-Used` headers when exceeded. Counter resets automatically each calendar month.

### Improved
- **Accuracy** — 93%+ on AI Act, 96%+ on DORA, 96%+ on GDPR, 97%+ on cross-regulation queries (200 evaluation questions). Improved abstention precision for out-of-scope questions (non-EU law, unrelated regulations).

### Security
- Removed unused configuration field that implied encryption-at-rest capability

---

## [0.5.4] - 2026-03-02

### Fixed
- **DORA query routing** — resolved collection routing issue that caused DORA regulation queries to fail
- **GDPR corpus sync** — production corpus updated to match full 644-chunk index (all 99 articles + 173 recitals)

### Verified
- All 3 regulations (AI Act, DORA, GDPR) confirmed operational with end-to-end query validation

---

## [0.5.3] - 2026-02-23

### Added
- **Structured JSON response mode** — `POST /v1/check` now accepts `response_format: "structured"` to return machine-readable fields: summary, legal basis, requirements, timeline, and cited articles
- **Confidence score** — `POST /v1/check` now returns a numeric `confidence_score` (0-1) alongside the text confidence level, computed from retrieval quality, citation density, and hedging signals
- **Public OpenAPI 3.1 spec** — full API schema at `api.gibs.dev/openapi.json` with typed request/response models for SDK and tool integration

### Improved
- **Faithfulness verification** — verification now checks against all retrieved sources instead of a truncated subset
- **Completeness checking** — single-aspect questions now run the full completeness pipeline instead of being skipped

---

## [0.5.2] - 2026-02-22

### Added
- **Knowledge graph** — cross-reference retrieval powered by a graph database with 2,550 nodes and 3,879 typed edges across all three regulations
- **AI agent discoverability** — `llms.txt` and AI-friendly `robots.txt` at gibs.dev for LLM and agent crawlers
- **MCP server authentication** — connections now require API key authorization

### Improved
- **GDPR corpus expanded** — full coverage of all 99 articles + 173 recitals (644 chunks, up from 276)
- **Retrieval precision** — graph-enhanced pipeline with multi-level enrichment and reranking reduces noise in single-regulation queries
- **Accuracy** — 96% on AI Act, 98% on GDPR, 95% on cross-regulation, 93% on DORA (200 expert-curated questions)

### Corpus
- AI Act: 836 chunks | GDPR: 644 chunks | DORA: 641 chunks
- Total: 2,121 indexed legal chunks across 3 regulations

---

## [0.5.1] - 2026-02-21

### Improved
- Upgraded synthesis engine — 93%+ accuracy across all three regulations (200+ evaluation questions)
- Verification pipeline optimization — reduced latency by skipping redundant checks on well-cited answers
- DORA corpus fully validated with expanded evaluation benchmark

### Security
- MCP server authentication — API key required on all connections
- Security headers on all API responses (HSTS, X-Content-Type-Options, X-Frame-Options, Referrer-Policy)
- Improved API key validation
- Request body size limits on optional fields
- Rate limiting resilience improvements

### Fixed
- MCP server disconnect handling — clean shutdown on client disconnection
- Demo streaming layout stability

---

## [0.5.0] - 2026-02-16

### Added
- **DORA regulation support** — Digital Operational Resilience Act: 64 articles + 12 delegated/implementing acts. Covers ICT risk management, incident reporting, TLPT, third-party oversight
- **Streaming responses** — `POST /v1/check/stream` for real-time synthesis via Server-Sent Events
- **MCP server listed** on MCP directories for AI agent discovery

### Improved
- Question routing — expanded keyword detection for DORA-specific terms (ICT risk, TLPT, financial entities)
- Evaluation infrastructure — retry on rate limits, configurable delay between requests
- 90.8% accuracy on 30-question DORA benchmark (96.7% abstention accuracy)

### Corpus
- AI Act: 113 articles, 13 annexes, 180 recitals
- GDPR: 99 articles, 173 recitals
- DORA: 64 articles + 12 delegated/implementing acts
- Three fully indexed EU regulations with cross-reference resolution

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
- AI Act: 113 articles, 13 annexes, 180 recitals
- GDPR: 99 articles, 173 recitals
- Full metadata enrichment on all sources

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
- 90-question evaluation benchmark
- Cross-reference resolution across regulations
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
