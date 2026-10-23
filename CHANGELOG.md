# Changelog

All notable changes to Retrieve-IQ will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- New multi-source federated search API endpoint
- Knowledge graph traversal with customizable relation filters
- Synthesis engine for generating answers from retrieved documents
- Support for additional vector database backends (Qdrant, Milvus)
- Enhanced rate limiting headers in all responses

### Changed
- Improved semantic search relevance scoring algorithm
- Updated vector embeddings to use latest model version
- Refactored API response structure for consistency

### Fixed
- Resolved issue with empty result sets for certain filter combinations
- Fixed memory leak in long-running vector indexing operations
- Corrected timezone handling in date range filters

## [2.1.0] - 2024-10-15

### Added
- **New Feature**: Graph-based entity relationship queries
  - Traverse knowledge graph with customizable depth and relations
  - Find related products, documents, or entities
  - Example: `{"entity_id": "prod-xyz", "depth": 2, "relations": ["related-products"]}`

- **SDK Updates**
  - TypeScript SDK v2.1.0 with improved type safety
  - Python package v2.1.0 with async support
  - Java SDK initial release
  - Go SDK in beta

- **Performance Improvements**
  - 40% faster query responses via new caching layer
  - Improved vector similarity search throughput (50,000 queries/s)
  - Reduced p99 latency from 78ms to 45ms for single queries

### Changed
- Search API v2 replaces legacy v0/v1 endpoints
- Response schema standardized across all endpoints
- Authentication now uses updated token format

### Deprecated
- Legacy search API v0 will be removed in v3.0
- Endpoint `/v1/legacy/*` redirects to new locations

### Fixed
- Fixed authentication header parsing in some SDKs
- Resolved issue with filter metadata not being indexed

## [2.0.0] - 2024-08-20

### Breaking Changes
- **New Auth Format**: API key must now be passed as Bearer token
  ```bash
  # Old (v1.x)
  curl ... -H "Authorization: Basic base64(api_key)"
  
  # New (v2.x)
  curl ... -H "Authorization: Bearer api_key"
  ```

- **Schema Updates**: Response objects now include metadata fields
  ```json
  {
    "results": [...],
    "meta": {
      "request_id": "...",
      "latency_ms": 45,
      "sources_queried": 3
    }
  }
  ```

### Added
- Official SDKs for all major languages
- Sandbox environment for development testing
- Comprehensive API documentation with examples
- Rate limiting headers in responses

### Removed
- Beta features moved to enterprise branch
- Unmaintained integrations deprecated

## [1.5.0] - 2024-06-10

### Security Updates
- Updated TLS configuration to require TLS 1.3 only
- Added stricter CSP headers
- Enhanced secret scanning in CI/CD pipeline

### Features
- Added multi-language support for search queries
- Improved relevance ranking with cross-encoder re-ranking
- New faceted search filters (metadata, date ranges)

## [1.0.0] - 2024-03-01

### Initial Public Release

Launched with:
- Core semantic search functionality
- Basic REST API endpoints
- JavaScript and Python SDKs
- Free and starter pricing tiers
- Community support channels

---

## Version History Notes

### Migration Guides

#### From v1.x to v2.0

See [Migration Guide v1→v2](docs/migration-v1v2.md):
- Update authentication header format
- Adjust response parsing for new metadata fields
- Review deprecated endpoint redirects

#### From v0.x to v1.0

Breaking changes in v1.0:
- Complete API redesign with consistent schemas
- New data model and response structures
- See [v0→v1 migration notes](docs/migration-v0v1.md)

### Unreleased Features

Upcoming in next release:
- OAuth2 authentication support
- Real-time webhook notifications
- Custom model training integration
- Advanced analytics dashboard

---

*For more detailed changelog history, see individual release notes on GitHub Releases.*
