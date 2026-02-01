---
name: vercel
description: Deploy and configure applications on Vercel. Use when deploying Next.js apps, configuring serverless functions, setting up edge functions, or managing Vercel projects.
license: MIT
source: hoodini/ai-agents-skills
---

# Vercel Deployment

## Quick Start

```bash
npm i -g vercel
vercel        # Preview deploy
vercel --prod # Production deploy
```

## Serverless Functions

```typescript
// api/hello.ts
import type { VercelRequest, VercelResponse } from '@vercel/node';

export default function handler(req: VercelRequest, res: VercelResponse) {
  const { name = 'World' } = req.query;
  res.status(200).json({ message: `Hello ${name}!` });
}
```

## Edge Functions

```typescript
// api/edge.ts
export const config = { runtime: 'edge' };

export default function handler(request: Request) {
  return new Response(JSON.stringify({ message: 'Hello from Edge!' }), {
    headers: { 'content-type': 'application/json' },
  });
}
```

## Vercel KV (Redis)

```typescript
import { kv } from '@vercel/kv';

await kv.set('user:123', { name: 'Alice' });
const user = await kv.get('user:123');
await kv.incr('user:123:visits');
```

## Vercel Postgres

```typescript
import { sql } from '@vercel/postgres';

const { rows } = await sql`SELECT * FROM users WHERE id = ${userId}`;
```

## ISR (Incremental Static Regeneration)

```typescript
// app/posts/[id]/page.tsx
export const revalidate = 60; // Revalidate every 60 seconds

export default async function Post({ params }) {
  const post = await getPost(params.id);
  return <div>{post.content}</div>;
}
```

## Resources

- **Vercel Docs**: https://vercel.com/docs
