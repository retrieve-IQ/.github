# Frequently Asked Questions (FAQ)

## Getting Started

### What is Retrieve-IQ?

Retrieve-IQ is an enterprise information retrieval platform that combines semantic search, knowledge graphs, and AI-powered synthesis to help organizations find, understand, and leverage their data efficiently.

### How do I get started with Retrieve-IQ?

1. Sign up at [retrieve-iq.com](https://retrieve-iq.com)
2. Generate an API key from your dashboard
3. Use our SDKs (Python, JavaScript, Java, Go) or make API calls directly
4. Explore our sandbox environment for testing

### Is there a free tier?

Yes! Our free tier includes:
- 1,000 search queries/month
- Access to demo data sources
- Sandbox API key for development
- Community support

Enterprise plans available upon request.

## API Usage

### What authentication methods do you support?

We support Bearer token authentication via API keys. OAuth2 is coming in Q4 2024.

### How long are API keys valid?

API keys expire after 24 hours for security. We recommend rotating them regularly or using our dashboard to extend validity.

### What data sources can I connect?

Supported integrations include:
- Document management systems (SharePoint, Google Drive, Dropbox)
- Databases (PostgreSQL, MySQL, MongoDB)
- API endpoints (REST/GraphQL)
- Cloud storage (AWS S3, Azure Blob, GCP Storage)
- Enterprise search engines (Elasticsearch, Solr)

See [Documentation](./INTEGRATIONS.md) for connector details.

### Can I use Retrieve-IQ offline?

No, Retrieve-IQ requires an internet connection to access our cloud APIs. We do provide self-hosting options for enterprise customers with on-prem deployments.

## Search & Retrieval

### What types of search do you offer?

We support:
- **Semantic Search**: Understanding intent and meaning (vector-based)
- **Keyword Search**: Exact term matching (BM25 algorithm)
- **Hybrid Search**: Combined approach with relevance scoring
- **Graph Search**: Relationship-based queries via knowledge graphs
- **Faceted Search**: Filter by metadata, date, source, etc.

### How do I improve search accuracy?

Tips for better results:
1. Use natural language queries ("users want password reset to work")
2. Add filters to narrow scope (`{"source": ["user-manual"]}`)
3. Combine semantic and keyword search modes
4. Review and provide feedback on search results
5. Ensure your data is well-indexed with proper metadata

### How do results get ranked?

We use multi-stage ranking:
1. **Initial Retrieval**: Vector similarity + keyword matching
2. **Re-ranking**: Cross-encoder scoring for precision
3. **Signal Boosting**: Recency, source authority, user preferences
4. **Diversity Filtering**: Remove near-duplicate results

### What languages does semantic search support?

Our models support:
- English (optimized)
- Spanish, French, German, Portuguese
- Chinese, Japanese, Korean
- And more via automatic translation

Check our [model card](https://retrieve-iq.com/models) for language-specific performance.

## Security & Privacy

### Is my data secure?

Yes! We follow security best practices:
- Encryption in transit (TLS 1.3) and at rest (AES-256)
- SOC 2 Type II compliant infrastructure
- Regular third-party security audits
- No data retention beyond necessary periods
- GDPR/CCPA-compliant data processing options

### Do you store my search queries?

We log minimal metadata for abuse prevention (IP, timestamp, query ID only). Full query text is not stored. Enterprise customers can configure no-retention policies.

### Can I deploy Retrieve-IQ on-premises?

Yes, enterprise customers can deploy our platform on their own infrastructure. Contact sales@retrieve-iq.com for licensing and deployment options.

## Pricing & Plans

### What are the pricing tiers?

| Tier | Monthly Queries | Storage | Support |
|------|-----------------|---------|---------|
| Free | 1,000 | 5 GB | Community |
| Starter | 50,000 | 50 GB | Email |
| Pro | 500,000 | 250 GB | Priority |
| Enterprise | Custom | Unlimited | Dedicated |

See [pricing page](https://retrieve-iq.com/pricing) for details.

### Are there hidden fees?

No hidden fees. Usage is based on:
- Number of search queries (rate-limited as shown above)
- Storage consumed by indexed data
- Premium model usage (optional upsell)
- API calls for vector embeddings (included up to plan limit)

## Troubleshooting

### Why am I getting empty results?

Common causes:
1. Query is too broad without filters
2. Data source connection broken or not synced
3. Filters are too restrictive
4. Using wrong data source for your query type

Test with: `{"query": "test", "limit": 5}` to verify basic functionality.

### Search is very slow on my queries

Optimization steps:
1. Reduce result set size (lower `limit`)
2. Narrow filters (`source`, `date_range`)
3. Consider caching frequent queries
4. Upgrade to Pro plan for dedicated infrastructure
5. Review [performance tuning](./PERFORMANCE.md) guide

### How do I debug API errors?

Enable verbose logging:
```bash
export RETRIEVE_IQ_DEBUG=true
curl -v https://api.retrieve-iq.com/v1/search ...
```

Look for:
- Request ID in responses (for support tickets)
- Error code and message in response body
- Rate limit headers if hitting quota limits

## Enterprise & Compliance

### Is Retrieve-IQ GDPR compliant?

Yes, we provide:
- Data residency options (EU data centers)
- Right to erasure functionality
- Data export capabilities
- DPA (Data Processing Agreement) on request

### Do you handle PII?

We include PII detection and masking features. Enterprise plans can configure:
- Automatic redaction before indexing
- PII-only data source segregation
- Audit logging for compliance audits

### What certifications do you have?

Our infrastructure is certified:
- SOC 2 Type II
- ISO 27001
- HIPAA-ready (for healthcare use cases)
- PCI-DSS Level 1

## Integration & Development

### Which programming languages have SDKs?

Available SDKs:
- JavaScript/TypeScript (`@retrieve-iq/sdk`)
- Python (`retrieve-iq` package)
- Java (SDK with Maven/Gradle support)
- Go (`github.com/retrieve-iq/go-sdk`)
- Ruby, PHP, and more coming soon

### Can I use my own embedding models?

Yes! Enterprise plans can:
- Deploy custom embedding models
- Use self-hosted vector stores
- Configure model endpoints
- Bring your own GPU instances

See [model deployment](./MODEL_CUSTOMIZATION.md) guide.

## Support & Community

### How do I get support?

Support channels by tier:
- **Free/Starter**: Community forums, documentation, email
- **Pro**: Email support (24-hour response), documented SLAs
- **Enterprise**: Dedicated account manager, phone support, custom SLA

### Can I contribute to Retrieve-IQ?

Yes! We welcome contributions:
- Report bugs via GitHub Issues
- Submit feature requests
- Contribute code (see [CONTRIBUTING.md](../CONTRIBUTING.md))
- Write documentation improvements

See our [contributor guidelines](../CONTRIBUTING.md) for details.

### Where can I discuss Retrieve-IQ with other users?

Join our community:
- GitHub Discussions: https://github.com/retrieve-IQ/.discussions
- Slack: register at retrieve-iq.com/slack
- Twitter: @RetrieveIQ

## Legal

### What license is Retrieve-IQ under?

The SDKs and client libraries are Apache 2.0 licensed (see LICENSE file). The core platform and data access terms are separate and require acceptance of our Terms of Service.

### Can I resell Retrieve-IQ to my customers?

No, you must use it directly or integrate via our supported methods. We do provide white-label solutions for enterprise partners under specific agreements.

## General

### When will new features be released?

Check our [roadmap](https://retrieve-iq.com/roadmap) and follow [@RetrieveIQ on Twitter](https://twitter.com/RetrieveIQ) for release announcements. Beta access is granted by request.

### Who owns my data?

You own all data you index and retrieve through Retrieve-IQ. We do not claim ownership of your indexed content, embeddings, or query results. Our terms specify this in detail.

---

*Still have questions? Email us at support@retrieve-iq.com or visit our [help center](https://help.retrieve-iq.com).*
