---
name: bun
description: Build fast applications with Bun JavaScript runtime. Use when creating Bun projects, using Bun APIs, bundling, testing, or as Node.js alternative.
license: MIT
source: hoodini/ai-agents-skills
---

# Bun - Fast JavaScript Runtime

## Quick Start

```bash
# Install
curl -fsSL https://bun.sh/install | bash

# Create project
bun init

# Run TypeScript directly
bun run index.ts

# Install packages
bun add express
```

## File I/O

```typescript
// Read files (super fast)
const file = Bun.file('data.json');
const content = await file.text();
const json = await file.json();

// Write files
await Bun.write('output.txt', 'Hello, Bun!');
await Bun.write('data.json', JSON.stringify({ key: 'value' }));
```

## HTTP Server

```typescript
Bun.serve({
  port: 3000,
  fetch(req) {
    const url = new URL(req.url);
    
    if (url.pathname === '/api/hello') {
      return Response.json({ message: 'Hello!' });
    }
    
    return new Response('Not Found', { status: 404 });
  },
});
```

## SQLite (Built-in)

```typescript
import { Database } from 'bun:sqlite';

const db = new Database('mydb.sqlite');

db.run(`CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)`);

const insert = db.prepare('INSERT INTO users (name) VALUES (?)');
insert.run('Alice');

const users = db.prepare('SELECT * FROM users').all();
```

## Password Hashing

```typescript
const hash = await Bun.password.hash('mypassword', {
  algorithm: 'argon2id',
});

const isValid = await Bun.password.verify('mypassword', hash);
```

## Testing

```typescript
import { describe, test, expect } from 'bun:test';

describe('Math', () => {
  test('addition', () => {
    expect(1 + 1).toBe(2);
  });
});
```

```bash
bun test
bun test --watch
bun test --coverage
```

## Bundler

```bash
bun build ./src/index.ts --outdir ./dist --minify
```

## Resources

- **Bun Docs**: https://bun.sh/docs
