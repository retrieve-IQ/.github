# Troubleshooting Guide

Common issues and their solutions when working with Retrieve-IQ.

## Search Not Returning Results

### Issue: Empty search results despite valid queries

**Checklist:**
1. Verify API key is active: `curl https://api.retrieve-iq.com/v1/health`
2. Check query formatting (remove special characters)
3. Ensure data sources are connected and synced
4. Verify filters aren't too restrictive

**Solution:**
```bash
# Test basic search without filters
curl -X POST https://api.retrieve-iq.com/v1/search \
  -H "Authorization: Bearer $API_KEY" \
  -d '{"query": "test", "limit": 5}'
```

## Authentication Errors

### Error: 401 Unauthorized

**Causes:**
- Expired API key (24-hour expiration)
- Incorrect header format
- Key not passed in request

**Fix:**
```bash
# Correct format
curl -H "Authorization: Bearer sk-live_xxxxx" https://api.retrieve-iq.com/...

# Check key expiry
curl https://api.retrieve-iq.com/v1/token-info \
  -H "Authorization: Bearer $API_KEY"
```

### Error: 403 Forbidden

**Causes:**
- Insufficient permissions for requested operation
- IP address not whitelisted
- Rate limit exceeded

**Fix:** Review API documentation for scope requirements.

## Performance Issues

### Issue: Slow query responses (>500ms)

**Diagnosis:**
```bash
# Enable verbose logging
export RETRIEVE_IQ_DEBUG=true
curl ... 2>&1 | grep -i latency
```

**Common bottlenecks:**
1. Large result sets (use `limit` parameter)
2. Complex filters affecting vector search
3. Missing indexes on frequently queried fields

**Optimization:**
```json
{
  "query": "...",
  "limit": 10,          // Don't fetch too many results
  "offset": 0,
  "filters": {
    "source": ["docs"], // Narrow scope
    "date_range": {"start": "2024-01-01"}
  }
}
```

## Vector Search Errors

### Error: "Index not found" or "Embedding failed"

**Causes:**
- Model endpoint unreachable
- Unsupported embedding dimension
- Rate limiting on vector service

**Fix:**
```bash
# Check model health
curl https://api.retrieve-iq.com/v1/vector/model-status

# Test embedding with default model
curl -X POST https://api.retrieve-iq.com/v1/vector/embed \
  -H "Authorization: Bearer $API_KEY" \
  -d '{"text": "test", "model": "default"}'
```

## Graph Traversal Issues

### Error: "No paths found" or timeout

**Diagnosis:**
```bash
# Check graph connectivity
curl -X GET https://api.retrieve-iq.com/v1/graph/health

# Test with simple query
curl -X POST https://api.retrieve-iq.com/v1/graph/traverse \
  -d '{"entity_id": "test-entity", "depth": 1}'
```

**Solutions:**
- Increase `max_depth` if relationships exist at deeper levels
- Reduce relation filter specificity
- Ensure entity IDs match exactly (case-sensitive)

## Synthesis Errors

### Error: "Context window exceeded" or "Synthesis failed"

**Fix:**
```json
{
  "query": "...",
  "retrieval_ids": ["id1", "id2"],
  "context_window": 4096,        // Increase if needed
  "output_format": "markdown"     // JSON for programmatic use
}
```

## Rate Limiting

### Response: 429 Too Many Requests

**Headers indicate:**
- `X-RateLimit-Limit`: Maximum requests allowed
- `X-RateLimit-Remaining`: Current quota
- `X-RateLimit-Reset`: When limit resets (timestamp)

**Mitigation:**
```javascript
// Implement exponential backoff
async function makeRequest(url, options) {
  const delay = Math.min(
    (Math.random() * 1000) + 500,  // Random delay to prevent thundering herd
    10000                           // Cap at 10 seconds
  );
  
  try {
    return await fetch(url, options);
  } catch (error) {
    if (error.status === 429) {
      console.log(`Rate limited, waiting ${delay}ms`);
      await sleep(delay);
      return makeRequest(url, options);
    }
    throw error;
  }
}
```

## Logging and Debugging

### Enable Verbose Logging

```bash
# Environment variable
export RETRIEVE_IQ_LOG_LEVEL=debug

# Or via request header
curl -H "X-Debug: true" ...
```

### Request ID Tracking

Each response includes a `request_id` for tracing:
```json
{
  "results": [...],
  "meta": {
    "request_id": "req_123abc456",
    "latency_ms": 45,
    ...
  }
}
```

Use this ID in support tickets and logs.

## Common Mistakes to Avoid

1. **Never log API keys** - They can be rotated via the dashboard
2. **Don't store embeddings locally** unless you have proper access controls
3. **Always validate response schemas** before processing
4. **Handle connection timeouts** gracefully (they're common)
5. **Test with sandbox environment** before production deployment

## Getting Support

If issues persist:

1. Check the [FAQ](./FAQ.md) first
2. Review error messages and request IDs in responses
3. Enable debug logging and capture request/response pairs
4. Contact support at: support@retrieve-iq.com

Include in your report:
- Request ID from failed response
- Steps to reproduce the issue
- Expected vs actual behavior
- Environment details (OS, SDK version)

## Additional Resources

- [API Reference](./API.md) - Complete endpoint documentation
- [Best Practices](./best-practices.md) - Optimization tips
- [Architecture](./ARCHITECTURE.md) - System design overview

---

*Last updated: 2024-10-20*
