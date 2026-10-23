# API Reference

## Authentication

All API requests require authentication via Bearer token:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" https://api.retrieve-iq.com/v1/...
```

### Token Management

- Tokens expire after 24 hours for security
- Rotate tokens using the management endpoint
- Support for OAuth2 flow for enterprise clients

## Endpoints

### Core Search API

#### POST /v1/search

Perform semantic search across connected data sources.

**Request:**
```json
{
  "query": "string",
  "limit": 10,
  "offset": 0,
  "filters": {
    "source": ["array"],
    "date_range": {"start": "ISO8601", "end": "ISO8601"},
    "metadata": {"key": "value"}
  },
  "rank_by": ["relevance", "date", "frequency"]
}
```

**Response:**
```json
{
  "results": [
    {
      "id": "string",
      "title": "string",
      "snippet": "string",
      "source": "string",
      "metadata": {"object"},
      "score": 0.95,
      "type": "document|image|data-point"
    }
  ],
  "meta": {
    "total": 150,
    "took_ms": 42,
    "sources_queried": 3
  }
}
```

#### POST /v1/search/multi

Perform federated search across multiple data sources.

**Request:**
```json
{
  "queries": [
    {"source": "docs", "query": "..."},
    {"source": "database", "query": "..."}
  ]
}
```

#### POST /v1/graph/traverse

Find related entities via knowledge graph traversal.

**Request:**
```json
{
  "entity_id": "string",
  "relations": ["related-to", "belongs-to"],
  "max_depth": 2,
  "filters": {
    "relation_type": "string"
  }
}
```

#### POST /v1/synthesize

Synthesize answers from multiple retrieved sources.

**Request:**
```json
{
  "query": "string",
  "retrieval_ids": ["id1", "id2", "id3"],
  "context_window": 4096,
  "output_format": "markdown|json|table"
}
```

### Vector Store API

#### POST /v1/vector/embed

Get vector embeddings for text content.

**Request:**
```json
{
  "text": "string or array of strings",
  "model": "default",
  "dimensions": 1536
}
```

#### POST /v1/vector/index

Add vectors to the store with metadata.

**Request:**
```json
{
  "vectors": [
    {"embedding": [...], "metadata": {...}, "id": "..."}
  ],
  "replace": false
}
```

### Knowledge Graph API

#### POST /v1/graph/ingest

Ingest structured data as graph triples.

**Request:**
```json
{
  "triples": [
    {
      "subject": "product-X",
      "predicate": "manufacturer",
      "object": "company-A"
    }
  ],
  "schema": {...}
}
```

#### POST /v1/graph/query

Run graph pattern queries.

**Request:**
```json
{
  "pattern": [
    {"subject": "?x", "predicate": "productOf", "object": "company-A"},
    {"subject": "?y", "predicate": "competitorOf", "object": "company-B"}
  ]
}
```

### Analytics API

#### GET /v1/analytics/overview

Get platform health and usage statistics.

**Response:**
```json
{
  "uptime_percent": 99.9,
  "queries_per_minute": 450,
  "cache_hit_rate": 0.87,
  "storage_used_gb": 1234.5,
  "active_connections": 128
}
```

#### GET /v1/analytics/performance

Get query performance metrics.

**Response:**
```json
{
  "latency_p50_ms": 12,
  "latency_p95_ms": 45,
  "latency_p99_ms": 78,
  "throughput_queries_per_sec": 320
}
```

## Rate Limiting

- Standard tier: 100 requests/minute
- Pro tier: 1,000 requests/minute  
- Enterprise: Custom limits via contract

Rate limit headers included in all responses:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: unix_timestamp
```

## Error Handling

All errors return consistent format:

```json
{
  "error": {
    "code": "INVALID_INPUT",
    "message": "Description of the error",
    "details": {
      "field": "query",
      "reason": "empty-string-not-allowed"
    },
    "request_id": "uuid"
  }
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| AUTH_REQUIRED | 401 | Missing or invalid authentication |
| INVALID_INPUT | 400 | Malformed request parameters |
| RATE_LIMITED | 429 | Too many requests |
| NOT_FOUND | 404 | Resource not found |
| INTERNAL_ERROR | 500 | Server-side error |

## SDKs & Client Libraries

Available SDKs:
- JavaScript/TypeScript (@retrieve-iq/sdk)
- Python (retrieve_iq)
- Java (retrieve-iq-java)
- Go (github.com/retrieve-iq/go-sdk)

```bash
npm install @retrieve-iq/sdk
pip install retrieve-iq
go get github.com/retrieve-iq/go-sdk
```

## Examples

### JavaScript/TypeScript Example

```javascript
import { RetrieveIQ } from '@retrieve-iq/sdk';

const client = new RetrieveIQ({ apiKey: process.env.RETRIEVE_IQ_API_KEY });

// Perform a search
const results = await client.search({
  query: 'machine learning best practices',
  limit: 10,
  sources: ['documentation', 'blog-posts']
});

console.log(results.results.map(r => r.title));
```

### Python Example

```python
from retrieve_iq import RetrieveIQClient

client = RetrieveIQClient(api_key="your-api-key")

# Search with filters
results = client.search(
    query="data pipeline optimization",
    limit=5,
    filters={
        "source": ["tutorials"],
        "date_from": "2024-01-01"
    }
)

for r in results.results:
    print(r['title'])
```

### cURL Examples

```bash
# Basic search
curl -X POST https://api.retrieve-iq.com/v1/search \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "customer onboarding flow",
    "limit": 5
  }'

# Graph traversal
curl -X POST https://api.retrieve-iq.com/v1/graph/traverse \
  -H "Authorization: Bearer $API_KEY" \
  -d '{"entity_id": "prod-xyz", "depth": 2}'

# Synthesis
curl -X POST https://api.retrieve-iq.com/v1/synthesize \
  -H "Authorization: Bearer $API_KEY" \
  -d '{
    "query": "how do I reset my password?",
    "retrieval_ids": ["doc-123", "doc-456"]
  }'
```

## Webhooks

Register for webhook notifications on events:

```bash
curl -X POST https://api.retrieve-iq.com/v1/webhooks \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": ["search.ready", "index.completed"],
    "callback_url": "https://your-app.com/webhook"
  }'
```

## Testing Your Integration

Use our sandbox environment for development:

```bash
export RETRIEVE_IQ_SANDBOX=true
export RETRIEVE_IQ_API_KEY=sandbox-key-xxxxx
```

Sandbox features:
- Free tier with generous limits
- Mock responses for testing error handling
- No production data access

## Versioning

API version is included in the URL path (`/v1/`). Breaking changes require a major version bump. Current stable version is v1.

## Changelog

See [CHANGELOG.md](../CHANGELOG.md) for API change history and migration guides.

---

*For enterprise API access, contact: enterprise@retrieve-iq.com*
