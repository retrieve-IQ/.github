# Examples & Code Samples

Practical code examples for using Retrieve-IQ in real applications.

## Search Basics

### JavaScript/TypeScript Example

```typescript
import { RetrieveIQ, SearchOptions } from '@retrieve-iq/sdk';

const client = new RetrieveIQ({
  apiKey: process.env.RETRIEVE_IQ_API_KEY!,
  base_url: 'https://api.retrieve-iq.com'
});

// Basic search
async function basicSearch() {
  const results = await client.search({
    query: 'how to reset my password',
    limit: 5
  });
  
  console.log('Top results:');
  for (const result of results.results) {
    console.log(`- ${result.title}`);
  }
}

// Advanced search with filters
async function advancedSearch() {
  const results = await client.search({
    query: 'machine learning best practices',
    limit: 10,
    offset: 0,
    rank_by: ['relevance', 'date'],
    filters: {
      source: ['documentation', 'blog-posts'],
      date_range: {
        start: '2024-01-01T00:00:00Z',
        end: '2024-10-20T23:59:59Z'
      },
      metadata: {
        category: ['tutorial', 'guide']
      }
    }
  });
  
  return results;
}

// Handling pagination
async function paginatedSearch() {
  const limit = 10;
  let offset = 0;
  
  do {
    const results = await client.search({
      query: 'user authentication',
      limit,
      offset
    });
    
    if (results.results.length === 0) break;
    
    // Process page of results
    await handleResults(results);
    
    offset += limit;
  } while (offset < results.meta.total);
}
```

### Python Example

```python
from retrieve_iq import RetrieveIQClient

client = RetrieveIQClient(
    api_key="your-api-key",
    sandbox=True  # Use sandbox for development
)

# Basic search
def basic_search():
    results = client.search(query="machine learning basics")
    print("Results:")
    for r in results.results:
        print(f"- {r.title}")
    return results

# Search with filters
def filtered_search():
    results = client.search(
        query="data pipeline optimization",
        limit=5,
        rank_by=["relevance", "date"],
        filters={
            "source": ["tutorials", "documentation"],
            "date_from": "2024-01-01"
        }
    )
    return results

# Async search in production
import asyncio

async def async_search():
    results = await client.search(query="async programming")
    print(f"Found {len(results.results)} results")
    
    # Stream results
    for chunk in results.stream():
        print(chunk.title)

# Run async
asyncio.run(async_search())
```

### Java Example

```java
import com.retrieveiq.client.RetrieveIQClient;
import java.util.concurrent.CompletableFuture;

public class SearchExample {
    private static final RetrieveIQClient client = new RetrieveIQClient(
        System.getenv("RETRIEVE_IQ_API_KEY")
    );

    public static void main(String[] args) throws Exception {
        // Basic search
        SearchResponse response = client.search(
            "machine learning fundamentals",
            SearchOptions.builder()
                .setLimit(10)
                .setRankBy(List.of("relevance", "date"))
                .build()
        );

        // Process results
        for (SearchResult result : response.results()) {
            System.out.println(result.title());
        }
    }
}
```

### Go Example

```go
package main

import (
	"context"
	"fmt"
	"github.com/retrieve-iq/go-sdk"
)

func main() {
	client := sdk.NewRetrieveIQClient(
		sdk.WithAPIKey("your-api-key"),
		sdk.WithBaseURL("https://api.retrieve-iq.com"),
	)

	// Basic search
	results, err := client.Search(context.Background(), &sdk.SearchRequest{
		Query: "machine learning basics",
		Limit: 10,
	})

	if err != nil {
		panic(err)
	}

	for _, r := range results.Results {
		fmt.Println(r.Title)
	}
}
```

## Graph Traversal Examples

### Find Related Entities

```typescript
// Find products related to a given product
async function findRelatedProducts(productId: string) {
  const result = await client.graphTraverse({
    entity_id: productId,
    depth: 2,
    relations: ['related-products', 'compatible-with'],
    filters: {
      relation_type: 'product'
    }
  });

  console.log('Related products:');
  for (const related of result.entities) {
    console.log(`- ${related.id}: ${related.name}`);
  }
}

// Build knowledge graph visualization
async function buildGraph() {
  const node = await client.graphGet({ entity_id: 'product-main' });
  
  const neighbors = await client.graphNeighbors({
    entity_id: node.id,
    depth: 1,
    relations: ['related-to', 'competitor-of']
  });

  return {
    center: node,
    relationships: neighbors.connections
  };
}
```

## Synthesis & Answer Generation

### Generate Answers from Retrieved Documents

```typescript
async function answerUserQuestion() {
  // Step 1: Search for relevant documents
  const searchResults = await client.search({
    query: "how do I deploy to production?",
    limit: 5
  });

  if (searchResults.results.length === 0) {
    return "No documentation found. Try a different query.";
  }

  // Step 2: Synthesize answer from retrieved documents
  const synthesized = await client.synthesize({
    query: "how do I deploy to production?",
    retrieval_ids: searchResults.results.map(r => r.id),
    context_window: 4096,
    output_format: 'markdown'
  });

  return synthesized.answer;
}

// Example response format
/*
{
  "answer": "To deploy your application to production using Retrieve-IQ:",
  "sources": [
    {
      "title": "Production Deployment Guide",
      "snippet": "...full deployment steps...",
      "confidence": 0.92
    }
  ],
  "alternative_answers": [...]
}
*/
```

## Batch Operations

### Search with Multiple Queries

```typescript
// Federated search across multiple data sources
async function federatedSearch() {
  const queries = [
    { source: 'documentation', query: 'authentication setup' },
    { source: 'blog-posts', query: 'best practices login' },
    { source: 'user-forums', query: 'login issues help' }
  ];

  const results = await client.multiSearch({
    queries
  });

  // Combine and rank all results
  let combinedResults = results.flat().sort((a, b) => 
    b.score - a.score
  );

  return combinedResults.slice(0, 20);
}
```

### Process Documents in Batch

```typescript
// Embed multiple documents at once for offline retrieval
async function batchEmbedding() {
  const documents = [
    { id: 'doc-1', content: "First document text...", metadata: {...} },
    { id: 'doc-2', content: "Second document text...", metadata: {...} }
  ];

  // Upload to vector store
  await client.vectorIndex({
    vectors: documents.map(d => ({
      embedding: client.embedDocument(d.content),
      id: d.id,
      metadata: d.metadata
    })),
    replace: false
  });
}
```

## Error Handling Patterns

### Robust Search Client

```typescript
class ResilientSearchClient {
  private readonly maxRetries = 3;
  private readonly backoffMs = 1000;

  async search(query: string): Promise<SearchResults> {
    let lastError: Error | null = null;

    for (let attempt = 0; attempt <= this.maxRetries; attempt++) {
      try {
        const results = await client.search({ query, limit: 10 });
        
        // If empty results, suggest search is working but data unavailable
        if (results.results.length === 0) {
          console.log(`No results for "${query}"`);
        }

        return results;
      } catch (error) {
        lastError = error;
        
        // Handle rate limiting with backoff
        if ((error as any).status === 429) {
          const delay = Math.min(this.backoffMs * (2 ** attempt), 30000);
          console.log(`Rate limited, retrying in ${delay}ms`);
          await sleep(delay);
          continue;
        }

        // Re-throws for non-retryable errors
        throw error;
      }
    }

    throw new Error(`Search failed after ${this.maxRetries + 1} attempts: ${lastError.message}`);
  }
}
```

## Integration with Frameworks

### Next.js Integration Example

```typescript
// pages/api/search.ts - API route wrapper
import { RetrieveIQ } from '@retrieve-iq/sdk';

export default async function handler(req, res) {
  const query = req.body.query || '';
  const client = new RetrieveIQ({ apiKey: process.env.RETRIEVE_IQ_API_KEY });

  try {
    const results = await client.search({
      query,
      limit: 10,
      rank_by: ['relevance', 'date']
    });

    res.status(200).json(results);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
}

// Component example with loading states
export default function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState(null);
  const [loading, setLoading] = useState(false);

  const handleSearch = async () => {
    if (!query) return;
    
    setLoading(true);
    try {
      const response = await fetch('/api/search', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ query })
      });
      
      const data = await response.json();
      setResults(data);
    } catch (error) {
      console.error('Search failed:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search documentation..."
      />
      <button onClick={handleSearch} disabled={loading}>
        Search {loading ? '...' : ''}
      </button>

      {results && (
        <div className="search-results">
          {results.results.map((r) => (
            <article key={r.id}>
              <h3>{r.title}</h3>
              <p>{r.snippet}</p>
              <a href={r.url}>{r.source}</a>
            </article>
          ))}
        </div>
      )}
    </div>
  );
}
```

### FastAPI Integration Example

```python
# app/main.py
from fastapi import FastAPI, HTTPException
from retrieve_iq import RetrieveIQClient
from pydantic import BaseModel
from typing import Optional

app = FastAPI(title="Retrieve-IQ Demo")

class SearchRequest(BaseModel):
    query: str
    limit: int = 10
    offset: int = 0

@app.get("/")
async def root():
    return {"message": "Welcome to Retrieve-IQ Demo API"}

@app.post("/search")
async def search(request: SearchRequest):
    client = RetrieveIQClient(api_key="your-api-key")
    
    try:
        results = await client.search(
            query=request.query,
            limit=request.limit,
            offset=request.offset
        )
        
        return {
            "query": request.query,
            "total": len(results.results),
            "results": [
                {
                    "id": r.id,
                    "title": r.title,
                    "snippet": r.snippet,
                    "score": r.score
                }
                for r in results.results
            ]
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="localhost", port=8000)
```

## Streaming & Real-time Updates

### Stream Large Result Sets

```typescript
// Stream results in chunks for large datasets
async function streamResults(query: string, limit: 500) {
  const client = new RetrieveIQClient({ apiKey });
  
  // First, get metadata about result set
  const meta = await client.searchMeta({ query, limit });
  
  if (meta.total > 1000) {
    console.warn(`Large result set (${meta.total} items), streaming...`);
  }

  // Stream results
  const stream = client.streamSearch({ query, limit });
  
  for await (const chunk of stream) {
    renderResult(chunk);
  }
}
```

## Advanced: Custom Ranking

### Implement Relevance Feedback

```typescript
async function trainWithFeedback() {
  const feedback = {
    query: "machine learning",
    positive_results: [
      { id: "doc-123" },  // User clicked this
      { id: "doc-456" }
    ],
    negative_results: [
      { id: "doc-789" }   // User skipped/declined
    ]
  };

  await client.rankTraining({
    session_id: "user-session-123",
    feedback,
    batch_size: 100  // Train after 100 interactions
  });
}
```

---

*These examples use the sandbox API key. Replace with your production key before deploying.*
