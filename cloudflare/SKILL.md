---
name: cloudflare
description: Build and deploy on Cloudflare's edge platform. Use when creating Workers, Pages, D1 databases, R2 storage, AI inference, or KV storage. Triggers on Cloudflare, Workers, D1, R2, KV, edge computing.
license: MIT
source: hoodini/ai-agents-skills
---

# Cloudflare Platform

Build globally distributed applications on Cloudflare's edge network.

## Quick Start

```bash
npm install -g wrangler
wrangler login
wrangler init my-worker
wrangler deploy
```

## Workers

```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    if (url.pathname === '/api/hello') {
      return Response.json({ message: 'Hello from the edge!' });
    }
    
    return new Response('Not Found', { status: 404 });
  },
};
```

## KV Storage

```typescript
// Set value
await env.MY_KV.put('key', 'value', { expirationTtl: 3600 });

// Get value
const value = await env.MY_KV.get('key');

// List keys
const list = await env.MY_KV.list({ prefix: 'user:' });
```

## D1 Database (SQLite)

```typescript
// Query
const { results } = await env.DB.prepare(
  'SELECT * FROM users WHERE id = ?'
).bind(1).all();

// Insert
await env.DB.prepare(
  'INSERT INTO users (name, email) VALUES (?, ?)'
).bind('Alice', 'alice@example.com').run();
```

## R2 Object Storage

```typescript
// Upload
await env.BUCKET.put(key, body, {
  httpMetadata: { contentType: 'application/json' }
});

// Download
const object = await env.BUCKET.get(key);

// Delete
await env.BUCKET.delete(key);
```

## Cloudflare AI

```typescript
const response = await env.AI.run('@cf/meta/llama-3-8b-instruct', {
  messages: [
    { role: 'system', content: 'You are a helpful assistant.' },
    { role: 'user', content: prompt },
  ],
});
```

## Resources

- **Workers Docs**: https://developers.cloudflare.com/workers/
- **D1 Docs**: https://developers.cloudflare.com/d1/
- **R2 Docs**: https://developers.cloudflare.com/r2/
