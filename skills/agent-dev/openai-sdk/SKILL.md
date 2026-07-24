---
name: openai-sdk
description: Integrate OpenAI APIs into any application. Use when a user says "call OpenAI", "use GPT", "OpenAI API", "chat completion", "streaming response", "function calling", "structured output", "embeddings", "OpenAI tools", "openai npm", "openai Python", or "token cost".
---

# OpenAI SDK

## What This Does

Implement OpenAI API integrations in Node.js and Python: chat completions, streaming, structured outputs, function/tool calling, embeddings, and image generation. Covers retry logic, token budgeting, cost estimation, and production best practices.

## Non-Negotiables

- **API key is server-side only.** Never expose `OPENAI_API_KEY` to the browser or client bundle.
- **Always set `max_tokens`.** Unbounded completions can run up unexpected costs.
- **Handle errors explicitly.** Rate limits, context window exceeded, and service outages all need distinct handling.
- **Stream long responses.** Any completion > 1 second of perceived wait should use streaming.
- **Log token usage.** Track `usage.prompt_tokens` and `usage.completion_tokens` per call.
- **Use structured outputs** (`response_format: { type: 'json_schema' }`) instead of prompting for JSON — it's guaranteed valid.

## Setup

```bash
# Node.js
npm install openai

# Python
pip install openai
```

```typescript
// lib/openai.ts
import OpenAI from 'openai'

export const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,    // server-side only
  maxRetries: 3,                          // auto-retry on 429 / 5xx
  timeout: 30_000,                        // 30s timeout
})
```

## Chat Completions

### Basic Completion

```typescript
const response = await openai.chat.completions.create({
  model:      'gpt-4o',
  max_tokens: 1024,
  messages: [
    { role: 'system', content: 'You are a concise technical assistant.' },
    { role: 'user',   content: userMessage }
  ],
  temperature: 0.7,
})

const text     = response.choices[0].message.content
const usage    = response.usage   // { prompt_tokens, completion_tokens, total_tokens }
const cost_est = (usage.total_tokens / 1_000_000) * 2.50  // gpt-4o: $2.50/M input tokens
```

### Streaming (Required for Long Outputs)

```typescript
// Next.js App Router streaming route
import { openai } from '@/lib/openai'
import { OpenAIStream, StreamingTextResponse } from 'ai'   // Vercel AI SDK

export async function POST(req: Request) {
  const { messages } = await req.json()

  const response = await openai.chat.completions.create({
    model:      'gpt-4o',
    max_tokens: 2048,
    stream:     true,
    messages,
  })

  const stream = OpenAIStream(response)
  return new StreamingTextResponse(stream)
}
```

```typescript
// Manual streaming with ioredis event handler
const stream = await openai.chat.completions.create({
  model: 'gpt-4o-mini',
  stream: true,
  messages: [{ role: 'user', content: prompt }],
})

let fullText = ''
for await (const chunk of stream) {
  const delta = chunk.choices[0]?.delta?.content ?? ''
  fullText   += delta
  process.stdout.write(delta)   // or send to SSE
}
```

## Structured Outputs

```typescript
import { zodResponseFormat } from 'openai/helpers/zod'
import { z } from 'zod'

const ProductSchema = z.object({
  name:        z.string(),
  price_cents: z.number().int().positive(),
  category:    z.enum(['electronics', 'clothing', 'food']),
  features:    z.array(z.string()).max(5),
})

const response = await openai.beta.chat.completions.parse({
  model:           'gpt-4o',
  max_tokens:      512,
  response_format: zodResponseFormat(ProductSchema, 'product'),
  messages: [
    {
      role:    'user',
      content: `Extract the product details from: "${rawText}"`
    }
  ],
})

const product = response.choices[0].message.parsed   // fully typed, validated
```

## Tool Calling (Function Calling)

```typescript
const tools = [
  {
    type: 'function' as const,
    function: {
      name:        'get_weather',
      description: 'Get current weather for a city',
      parameters: {
        type: 'object',
        properties: {
          city:    { type: 'string', description: 'City name' },
          country: { type: 'string', description: 'ISO 3166-1 alpha-2 country code' }
        },
        required: ['city']
      }
    }
  }
]

const messages = [{ role: 'user', content: "What's the weather in Tokyo?" }]

// Step 1: Initial call
let response = await openai.chat.completions.create({
  model: 'gpt-4o', max_tokens: 512, tools, messages
})

// Step 2: Execute tool if requested
while (response.choices[0].finish_reason === 'tool_calls') {
  const toolCalls = response.choices[0].message.tool_calls!
  messages.push(response.choices[0].message)

  for (const call of toolCalls) {
    const args   = JSON.parse(call.function.arguments)
    const result = await getWeather(args.city, args.country)  // your function
    messages.push({
      role:         'tool',
      tool_call_id: call.id,
      content:      JSON.stringify(result)
    })
  }

  // Step 3: Continue conversation with tool results
  response = await openai.chat.completions.create({
    model: 'gpt-4o', max_tokens: 512, tools, messages
  })
}

console.log(response.choices[0].message.content)
```

## Embeddings

```typescript
// Generate embedding for semantic search / RAG
async function embed(text: string): Promise<number[]> {
  const response = await openai.embeddings.create({
    model: 'text-embedding-3-small',   // 1536 dims, cheapest
    input: text.slice(0, 8_191),       // max input length
  })
  return response.data[0].embedding
}

// Cosine similarity for nearest-neighbor search
function cosineSimilarity(a: number[], b: number[]): number {
  const dot = a.reduce((sum, ai, i) => sum + ai * b[i], 0)
  const magA = Math.sqrt(a.reduce((sum, ai) => sum + ai * ai, 0))
  const magB = Math.sqrt(b.reduce((sum, bi) => sum + bi * bi, 0))
  return dot / (magA * magB)
}
```

## Error Handling

```typescript
import { APIError, RateLimitError, APIConnectionError } from 'openai'

try {
  const response = await openai.chat.completions.create({ ...params })
  return response
} catch (err) {
  if (err instanceof RateLimitError) {
    // Retry after backoff — SDK handles this automatically with maxRetries
    throw new Error('Rate limit reached. Please try again later.')
  }
  if (err instanceof APIError) {
    if (err.status === 400 && err.message.includes('context_length')) {
      throw new Error('Input is too long. Please shorten your message.')
    }
    if (err.status === 503) {
      throw new Error('OpenAI service is temporarily unavailable.')
    }
    console.error('OpenAI API error:', err.status, err.message)
  }
  throw err
}
```

## Model Selection Guide

| Model | Best For | Cost (per 1M tokens) |
|-------|----------|---------------------|
| `gpt-4o` | Complex reasoning, tool use, structured output | $2.50 in / $10 out |
| `gpt-4o-mini` | Simple tasks, high volume | $0.15 in / $0.60 out |
| `o1` | Multi-step reasoning, math, code | $15 in / $60 out |
| `o1-mini` | Code generation, STEM | $3 in / $12 out |
| `text-embedding-3-small` | Embeddings, RAG | $0.02 per 1M tokens |
| `dall-e-3` | Image generation | $0.04–$0.08/image |

## Token Budgeting

```typescript
// Estimate tokens before call (rough: 1 token ≈ 4 chars)
function estimateTokens(text: string): number {
  return Math.ceil(text.length / 4)
}

// Use tiktoken for accurate counts
import { encoding_for_model } from 'tiktoken'

const enc    = encoding_for_model('gpt-4o')
const tokens = enc.encode(prompt).length
enc.free()
```

## Production Checklist

- [ ] `OPENAI_API_KEY` in server environment only (not `.env.local` committed to git)
- [ ] `max_tokens` set on every call
- [ ] Streaming enabled for responses > ~500 tokens
- [ ] Structured outputs used instead of "return JSON" prompts
- [ ] `usage` logged per call for cost tracking
- [ ] Error handling covers RateLimitError, context_length, and 503
- [ ] Retry logic in place (SDK `maxRetries: 3` handles most cases)
- [ ] Model chosen based on task complexity, not defaulting to most expensive
