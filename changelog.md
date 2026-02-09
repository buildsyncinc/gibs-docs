# Changelog

All notable changes to the Gibs API.

---

## [Unreleased]

### Coming Soon
- Python SDK (`pip install gibs`)
- JavaScript SDK (`npm install @gibs/client`)
- Webhook notifications for corpus updates

---

## [0.1.0] - 2025-02-10

### Added
- Initial API release
- `POST /v1/classify` - AI system risk classification
- `POST /v1/check` - Compliance Q&A with citations
- `GET /health` - Health check endpoint
- EU AI Act corpus (113 articles, 13 annexes)
- GDPR selected articles
- Commission guidelines integration
- MCP server for Claude/Cursor integration

### Corpus
- Version: v0.1.0
- Sources: EU AI Act (2024/1689), GDPR (2016/679), Commission Guidelines
- Total chunks: ~600

---

## Version Format

We use [Semantic Versioning](https://semver.org/):
- **MAJOR**: Breaking API changes
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, corpus updates

Corpus versions are tracked separately and included in API responses.
