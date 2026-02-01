---
name: llm-application-dev
description: Building applications with Large Language Models - prompt engineering, RAG patterns, and LLM integration. Use for AI-powered features, chatbots, or LLM-based automation.
license: MIT
source: skillcreatorai/Ai-Agent-Skills
---

# LLM Application Development

## Prompt Engineering

### Structured Prompts
```typescript
const systemPrompt = `You are a helpful assistant.

RULES:
- Only answer questions about our product
- If you don't know, say "I don't know"
- Keep responses concise (under 100 words)

CONTEXT:
{context}`;
```

### Few-Shot Examples
```typescript
const prompt = `Classify sentiment:

Examples:
Input: "Love this product!"
Output: positive

Input: "Worst purchase ever"
Output: negative

Input: "${customerFeedback}"
Output:`;
```

## API Integration

### OpenAI
```typescript
import OpenAI from 'openai';

const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function chat(messages: Message[]): Promise<string> {
  const response = await openai.chat.completions.create({
    model: 'gpt-4',
    messages,
    temperature: 0.7,
  });
  return response.choices[0].message.content ?? '';
}
```

### Anthropic
```typescript
import Anthropic from '@anthropic-ai/sdk';

const anthropic = new Anthropic();

const response = await anthropic.messages.create({
  model: 'claude-3-opus-20240229',
  max_tokens: 1024,
  messages: [{ role: 'user', content: prompt }],
});
```

## RAG Pipeline

```typescript
async function ragQuery(question: string): Promise<string> {
  // 1. Embed the question
  const embedding = await embedText(question);

  // 2. Search vector database
  const docs = await vectorDb.search(embedding, { limit: 5 });

  // 3. Build context
  const context = docs.map(d => d.content).join('\n\n');

  // 4. Generate answer
  return await chat(`Context:\n${context}\n\nQuestion: ${question}`);
}
```

## Error Handling

```typescript
async function safeLLMCall<T>(fn: () => Promise<T>, retries = 3): Promise<T> {
  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (error.status === 429) {
        await sleep(Math.pow(2, i) * 1000);
        continue;
      }
      throw error;
    }
  }
  throw new Error('Max retries exceeded');
}
```

## Best Practices

- **Token Management**: Track usage and set limits
- **Caching**: Cache embeddings and common queries
- **Guardrails**: Validate outputs before using
- **Logging**: Log prompts and responses for debugging
