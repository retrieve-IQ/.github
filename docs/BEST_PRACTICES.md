# Best Practices

Tips and recommendations for optimal use of Retrieve-IQ in production environments.

## Search Query Design

### Writing Effective Queries

**Do:**
- Use natural language ("users can't reset passwords")
- Be specific about context ("authentication flow enterprise SSO")
- Specify what you need ("how to configure OAuth with Okta")

**Don't:**
- Use overly generic queries ("help", "information")
- Rely solely on keywords without semantic context
- Chain multiple unrelated concepts in one query

### Query Patterns

```json
// ✅ Good - Clear intent
{"query": "production deployment checklist for Python microservices"}

// ⚠️ Better with filters  
{"query": "error handling patterns", 
 "filters": {"source": ["tutorials"]}}

// ❌ Poor - Too broad
{"query": "help me please"}
```

### Handling Ambiguity

When queries could mean different things:
1. Disambiguate in your UI before sending
2. Use filters to narrow scope
3. Provide fallback suggestions

## Performance Optimization

### Caching Strategies

```typescript
// Implement client-side caching for common queries
const queryCache = new Map<string, Promise<SearchResults>>();

async function cachedSearch(query: string) {
  if (queryCache.has(query)) {
    return queryCache.get(query);
  }
  
  const promise = client.search({ query });
  queryCache.set(query, promise);
  return promise;
}

// Clear cache on user input change or session boundary
function clearCache() {
  queryCache.clear();
}
```

### Batch Processing

For large result sets, consider streaming:

```typescript
// Stream instead of fetching all at once
async function processLargeResults(query: string) {
  const stream = client.streamSearch({ query, limit: 50 });
  
  for await (const chunk of stream) {
    // Process each chunk as it arrives
    await renderChunk(chunk);
    
    // Show loading indicator while streaming continues
    if (isStreaming) {
      await showProgress();
    }
  }
}
```

### Rate Limiting Best Practices

```javascript
// Implement exponential backoff with jitter
async function searchWithRetry(query, maxRetries = 3) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      return await client.search({ query });
    } catch (error) {
      if (error.status === 429) {
        // Rate limited - wait with exponential backoff + jitter
        const delay = Math.min(
          Math.pow(2, attempt) * 1000 + Math.random() * 500,
          30000
        );
        await sleep(delay);
      } else {
        throw error; // Non-retryable error
      }
    }
  }
}
```

## Data Quality Tips

### Indexing Best Practices

1. **Metadata is important**: Always include useful metadata when indexing
   ```json
   {
     "title": "Password Reset Guide",
     "metadata": {
       "category": "security",
       "audience": ["admin", "developer"],
       "version": "2.0",
       "last_reviewed": "2024-10-15"
     }
   }
   ```

2. **Keep documents up-to-date**: Set expiry dates for documentation
3. **Version your content**: Use metadata to track content versions
4. **Test retrieval accuracy** before going live

### Filtering Strategies

```json
// ✅ Effective filters
{
  "filters": {
    "source": ["user-manual", "api-docs"],
    "date_range": {"start": "2024-06-01"},
    "metadata": {
      "category": ["troubleshooting"]
    }
  }
}

// ❌ Ineffective - too restrictive
{
  "filters": {
    "source": ["very-specific-old-doc"],
    "exact_phrase": [rarely_used_term]
  }
}
```

## Security Considerations

### API Key Management

```bash
# ❌ Never do this
export RETRIEVE_IQ_API_KEY="sk-prod-xxxxx"  # In environment file!
git add .env                                  # Accidentally committed!

# ✅ Do this instead
echo "RETRIEVE_IQ_API_KEY=$API_KEY" >> ~/.netrc  # Secure storage
```

### Request Validation

Always validate user input before passing to API:

```typescript
function sanitizeQuery(input: string): { query: string; error?: string } {
  const max_length = 500; // Prevent DoS
  
  let query = input;
  
  // Escape dangerous characters
  query = query
    .replace(/[<>]/g, '')   // Remove angle brackets
    .replace(/javascript:/gi, '')  // Block javascript: URIs
    
  if (query.length > max_length) {
    return { error: 'Query too long' };
  }
  
  return { query };
}
```

## Monitoring & Observability

### Key Metrics to Track

- Query latency percentiles (p50, p95, p99)
- Cache hit rates
- Error rates by endpoint
- Throughput per service
- User search success rate

### Logging Best Practices

```typescript
// ✅ Structured logging with context
const logger = createLogger('retrieve-iq');

async function logSearch(search: SearchRequest) {
  const startTime = Date.now();
  
  try {
    const results = await client.search(search);
    
    logger.info('search.completed', {
      request_id: search.requestId,
      query_length: search.query.length,
      result_count: results.results.length,
      latency_ms: Date.now() - startTime,
      success: true
    });
    
    return results;
  } catch (error) {
    logger.error('search.failed', {
      request_id: search.requestId,
      error_message: error.message,
      stack: error.stack,
      latency_ms: Date.now() - startTime
    });
    
    throw error;
  }
}
```

## Accessibility Guidelines

### UI Considerations

- Ensure proper ARIA labels on search forms
- Provide keyboard navigation (Tab to focus, Enter to search)
- Use semantic HTML for results structure
- Include skip-to-content links
- Support screen reader announcements

### Color Contrast

Always use sufficient color contrast:
```css
/* ✅ Accessible - 7:1 contrast ratio */
.primary-button {
  background-color: #0066cc; /* Blue on white */
  color: #ffffff;
}

/* ❌ Not accessible - low contrast */
.weak-link {
  color: #777; /* Gray text on white */
}
```

## Internationalization

### Supporting Multiple Languages

```typescript
// Use i18n for user-facing strings
const SEARCH_PLACEHOLDER = t('search.placeholder', 'Search documentation...');
const NO_RESULTS = t('search.no_results', 'No results found. Try different terms.');

// Don't hardcode strings
function searchForm() {
  return (
    <form>
      <input 
        placeholder={SEARCH_PLACEHOLDER}
        aria-label="Search query input"
      />
    </form>
  );
}
```

## Cost Optimization

### Reducing API Costs

1. **Cache frequent queries**: Implement LRU cache for common searches
2. **De-duplicate embeddings**: Use cosine similarity threshold before indexing new documents
3. **Batch embed operations**: Process multiple items together
4. **Use appropriate result limits**: Don't over-fetch if you don't need it

### Monitoring Usage Costs

```typescript
// Track your usage to stay within plan limits
async function trackUsage() {
  const usage = await client.getUsageStats();
  
  // Alert before hitting 80% of quota
  if (usage.current / usage.plan_limit > 0.8) {
    alert('Approaching API limit - consider upgrading plan');
  }
}
```

## Error Recovery Patterns

### Graceful Degradation

```typescript
class FallbackSearchClient {
  async search(query: string): Promise<SearchResults> {
    try {
      // Try main service first
      return await this.mainService.search({ query });
    } catch (error) {
      // Fallback to secondary endpoint
      console.warn('Main service unavailable, using fallback');
      return await this.fallbackService.search({ query });
    }
  }
}
```

### User-Friendly Error Messages

```typescript
function translateError(error: APIError): string {
  switch (error.status) {
    case 429:
      return "Too many requests. Please try again in a moment.";
    case 401:
      return "Your session expired. Please sign in again.";
    case 503:
      return "Our services are currently experiencing high demand.";
    default:
      return error.message;
  }
}
```

## Testing Recommendations

### Unit Tests

Test your integration layer thoroughly:

```typescript
describe('SearchClient', () => {
  it('returns empty array for unknown query', async () => {
    const client = new RetrieveIQClient({ apiKey });
    const results = await client.search({ query: 'unknown-topic' });
    
    expect(results.results).toHaveLength(0);
  });
  
  it('handles rate limiting gracefully', async () => {
    // Test retry logic with exponential backoff
  });
});
```

### Load Testing

Test your integration under load:

```bash
# Use k6 or similar to test search performance
k6 run search_test.js

# Example k6 script:
import http from 'k6/http';

export const options = {
  vus: 100,      // Virtual users
  duration: '5m'
};

export default function () {
  const res = http.get('https://api.retrieve-iq.com/v1/health');
}
```

---

*Keep this document updated as the platform evolves. Submit improvements via GitHub Issues.*
