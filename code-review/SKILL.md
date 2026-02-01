---
name: code-review
description: Automated code review for pull requests using specialized review patterns. Analyzes code for quality, security, performance, and best practices. Use when reviewing code changes, PRs, or doing code audits.
license: Apache-2.0
source: skillcreatorai/Ai-Agent-Skills
---

# Code Review

## Review Categories

### 1. Security Review
Check for:
- SQL injection vulnerabilities
- XSS (Cross-Site Scripting)
- Command injection
- Insecure deserialization
- Hardcoded secrets/credentials
- Improper authentication/authorization
- Insecure direct object references

### 2. Performance Review
Check for:
- N+1 queries
- Missing database indexes
- Unnecessary re-renders (React)
- Memory leaks
- Blocking operations in async code
- Missing caching opportunities
- Large bundle sizes

### 3. Code Quality Review
Check for:
- Code duplication (DRY violations)
- Functions doing too much (SRP violations)
- Deep nesting / complex conditionals
- Magic numbers/strings
- Poor naming
- Missing error handling
- Incomplete type coverage

### 4. Testing Review
Check for:
- Missing test coverage for new code
- Tests that don't test behavior
- Flaky test patterns
- Missing edge cases
- Mocked external dependencies

## Review Output Format

```markdown
## Code Review Summary

### 🔴 Critical (Must Fix)
- **[File:Line]** [Issue description]
  - **Why:** [Explanation]
  - **Fix:** [Suggested fix]

### 🟡 Suggestions (Should Consider)
- **[File:Line]** [Issue description]
  - **Why:** [Explanation]
  - **Fix:** [Suggested fix]

### 🟢 Nits (Optional)
- **[File:Line]** [Minor suggestion]

### ✅ What's Good
- [Positive feedback on good patterns]
```

## Common Patterns to Flag

### Security
```javascript
// BAD: SQL injection
const query = `SELECT * FROM users WHERE id = ${userId}`;

// GOOD: Parameterized query
const query = 'SELECT * FROM users WHERE id = $1';
await db.query(query, [userId]);
```

### Performance
```javascript
// BAD: N+1 query
users.forEach(async user => {
  const posts = await getPosts(user.id);
});

// GOOD: Batch query
const userIds = users.map(u => u.id);
const posts = await getPostsForUsers(userIds);
```

### Error Handling
```javascript
// BAD: Swallowing errors
try {
  await riskyOperation();
} catch (e) {}

// GOOD: Handle or propagate
try {
  await riskyOperation();
} catch (e) {
  logger.error('Operation failed', { error: e });
  throw new AppError('Operation failed', { cause: e });
}
```

### React Performance
```javascript
// BAD: Creating new objects in render
<Component style={{ color: 'red' }} />

// GOOD: Memoize or use constants
const style = useMemo(() => ({ color: 'red' }), []);
<Component style={style} />

// BAD: Missing dependency in useEffect
useEffect(() => {
  fetchData(userId);
}, []); // Missing userId

// GOOD: Include all dependencies
useEffect(() => {
  fetchData(userId);
}, [userId]);
```

### TypeScript
```typescript
// BAD: Using 'any'
function process(data: any) { ... }

// GOOD: Proper typing
interface UserData {
  id: string;
  name: string;
}
function process(data: UserData) { ... }

// BAD: Non-null assertion abuse
const name = user!.profile!.name!;

// GOOD: Proper null checks
const name = user?.profile?.name ?? 'Unknown';
```

## Review Checklist

### Security
- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] Input validation present for all user inputs
- [ ] SQL queries use parameterized statements
- [ ] File uploads validated (type, size, content)
- [ ] Authentication/authorization on all protected routes

### Performance
- [ ] No N+1 queries
- [ ] Proper pagination for large datasets
- [ ] Images optimized and lazy loaded
- [ ] No memory leaks (event listeners, subscriptions)
- [ ] Expensive operations are memoized/cached

### Code Quality
- [ ] No code duplication (DRY)
- [ ] Functions have single responsibility
- [ ] Clear and descriptive naming
- [ ] Proper error handling with meaningful messages
- [ ] Types/interfaces defined for all data structures

### Testing
- [ ] Tests added for new functionality
- [ ] Edge cases covered
- [ ] Tests are deterministic (no flaky tests)
- [ ] Mocks are minimal and necessary

### Documentation
- [ ] Complex logic has comments
- [ ] Public APIs have JSDoc/docstrings
- [ ] Breaking changes documented
- [ ] README updated if needed

## Severity Guidelines

| Severity | Criteria | Action |
|----------|----------|--------|
| 🔴 Critical | Security vulnerabilities, data loss risk, crashes | Must fix before merge |
| 🟡 Important | Performance issues, maintainability concerns | Should fix, can defer |
| 🟢 Minor | Style, naming, minor improvements | Optional, nice to have |

## Quick Commands

```bash
# Run linter
npm run lint

# Run type check
npm run typecheck

# Run tests with coverage
npm run test -- --coverage

# Check bundle size
npm run build && npm run analyze
```
