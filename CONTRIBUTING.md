# Contributing to Retrieve-IQ

Thank you for your interest in contributing to Retrieve-IQ! This document outlines the contribution process and guidelines.

## How to Contribute

### Reporting Bugs

1. Check existing issues on [GitHub Issues](https://github.com/retrieve-IQ/.issues)
2. Create a new issue with:
   - Clear title and description
   - Steps to reproduce (if applicable)
   - Expected vs actual behavior
   - Environment details (OS, version, etc.)
3. Use relevant labels when possible

### Suggesting Features

1. Check [GitHub Discussions](https://github.com/retrieve-IQ/.discussions) for feature discussions
2. Open a feature request issue with:
   - Problem statement
   - Proposed solution
   - Use case examples
   - Alternatives considered

### Pull Requests

We welcome pull requests! Here's the workflow:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Write or update tests
5. Ensure all CI checks pass
6. Submit a pull request with a clear description

## Development Setup

### Prerequisites

- Node.js 18+ or Python 3.9+
- Git
- npm/yarn/pip package manager

### Getting Started

```bash
# Clone the repository
git clone https://github.com/retrieve-IQ/.github.git
cd .github

# Install dependencies
npm install        # or: pip install -r requirements.txt

# Start development server
npm run dev        # or: python -m uvicorn app.main:app --reload
```

## Code Style Guidelines

### General Principles

- Write clear, concise code with meaningful names
- Keep functions small and focused (single responsibility)
- Add comments for complex logic, not obvious operations
- Prefer composition over inheritance
- Test before committing

### Type Safety

All TypeScript files must include type annotations. Use generics when appropriate.

```typescript
// ✅ Good
function processItems<T>(items: T[], transform: (item: T) => T): T[] {
  return items.map(transform);
}

// ❌ Bad
function processItems(items, transform) { ... }
```

### Documentation

Use JSDoc comments for public APIs:

```typescript
/**
 * Searches the knowledge base for relevant documents.
 * @param query - The search query string
 * @param options - Search configuration options
 * @returns Promise resolving to search results
 * @throws {Error} When the search fails
 */
async function search(query: string, options?: SearchOptions): Promise<SearchResults> { ... }
```

## Testing Guidelines

### Test Requirements

- All new features must include tests
- Maintain ≥90% code coverage
- Tests must be deterministic and isolated

### Test Structure

```typescript
describe('SearchService', () => {
  it('should return relevant results for semantic queries', async () => {
    const service = createTestService();
    const results = await service.search('machine learning');
    expect(results.results.length).toBeGreaterThan(0);
    expect(results.results[0].score).toBeCloseTo(0.85, 1);
  });

  it('should handle empty queries gracefully', async () => {
    const service = createTestService();
    await expect(service.search('')).rejects.toThrow('Empty query not allowed');
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run with coverage report
npm run test:coverage

# Run specific test file
npm test -- search.spec.ts
```

## Code Review Process

All pull requests require review and approval:

1. Self-review your changes before submitting
2. Ensure CI pipeline passes
3. Address reviewer feedback promptly
4. Keep PRs focused (one feature per PR)
5. Update relevant documentation

### Review Checklist

- [ ] Code follows style guidelines
- [ ] Tests added for new functionality
- [ ] Documentation updated
- [ ] No console.log or debugger statements
- [ ] Changelog entry added (if applicable)

## Branch Naming Convention

Use descriptive branch names:

```
feature/add-vector-search
bugfix/fix-memory-leak
chore/update-dependencies
docs/improve-readme
refactor/simplify-pipeline
```

## Commit Message Guidelines

Follow [Conventional Commits](https://www.conventionalcommits.org):

```
<type>(<scope>): <subject>

<body> (optional)

<footer> (optional, for breaking changes)
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, missing semi colons, etc
- `refactor`: Code change that neither fixes bug nor adds feature
- `test`: Adding/test-fixes
- `chore`: Build process or auxiliary tool changes

Examples:

```
feat(search): add semantic search endpoint

Implements vector similarity search using Qdrant vector DB.

BREAKING CHANGE: Removes legacy search API v0
```

## Documentation Standards

### Writing Docs

1. Keep documentation in `/docs` folder
2. Use markdown with clear headings
3. Include code examples
4. Link to related documentation
5. Keep it up-to-date with code changes

### Code Comments

- Comment WHY, not WHAT (code should speak for itself)
- Explain complex algorithms or business logic
- Document known limitations or TODOs

```typescript
// ✅ Good - explains the why
// Use HNSW index for sub-linear nearest neighbor search
const hnswIndex = await VectorStore.buildHNSW({ ... });

// ❌ Bad - obvious operation
let total = 0;
for (const item of items) {
  total += item.value;
}
```

## Performance Guidelines

### Best Practices

- **Database Queries**: Add indexes where appropriate
- **API Endpoints**: Aim for <100ms p95 latency
- **Memory**: Monitor heap growth, avoid leaks
- **Caching**: Use Redis for expensive computations
- **Vector Operations**: Batch processing when possible

### Profiling Tools

```bash
# Node.js profiling
npm run profile -- bundle-profile

# Python cProfile
python -m cProfile -o profile.out app.py

# Memory profiling
npm run memory-report
```

## Security Considerations

### Principles

- Never commit secrets or API keys
- Use environment variables for sensitive data
- Validate all user inputs
- Sanitize outputs to prevent XSS
- Follow OWASP guidelines

### Secret Management

```typescript
// ✅ Good - use env vars
const apiKey = process.env.API_KEY;

// ❌ Bad - hardcoded
const apiKey = 'sk-1234567890abcdef';
```

See `.env.example` for expected environment variables.

## Accessibility Requirements

All UI components must meet WCAG 2.1 AA standards:

- Use semantic HTML
- Proper ARIA labels
- Keyboard navigation support
- Color contrast ≥4.5:1
- Provide alt text for images

## Accessibility Testing

```bash
# Run accessibility tests
npm run a11y:check

# Lighthouse accessibility audit
npm run lint:lighthouse
```

## Internationalization (i18n)

All user-facing strings must support multiple languages:

```typescript
// ✅ Good - use i18n
const title = t('dashboard.title');

// ❌ Bad - hardcoded strings
const title = 'Dashboard';
```

Supported languages will be listed in `i18n/config.json`.

## License Compliance

- Verify all dependencies have appropriate licenses
- Add notices for third-party code
- Check LICENSE file before contributing

## Performance Benchmarks

Target performance metrics:

| Operation | P95 Latency | P99 Latency |
|-----------|-------------|-------------|
| Search query | <50ms | <100ms |
| Document retrieval | <20ms | <50ms |
| Graph traversal | <30ms | <80ms |

## Code of Conduct

We strive to maintain a welcoming and inclusive community:

- Be respectful and professional
- Welcome newcomers with patience
- Focus on constructive criticism
- Assume good intentions

See our full [Code of Conduct](CODE_OF_CONDUCT.md) for more details.

## Getting Help

Need assistance? Reach out through:

- **GitHub Discussions**: https://github.com/retrieve-IQ/.discussions
- **Email**: contributors@retrieve-iq.com
- **Slack**: Join via retrieve-iq.com/slack

## Contributor License Agreement

By contributing, you agree that your contributions will be licensed under the project's Apache 2.0 license. Your employment relationship with any company, if applicable, remains unchanged.

## Recognition

All contributors are recognized:

- Credits in README
- [CONTRIBUTORS.md](CONTRIBUTORS.md) listing
- Release notes mention for significant contributions

---

**Thank you for contributing to Retrieve-IQ!** 🎉

Your help makes our platform better for everyone.
