---
name: redis
description: Add Redis caching, pub/sub messaging, job queues, session storage, and rate limiting to any application. Use when a user says "add caching", "Redis", "cache this API", "rate limiting", "pub/sub", "job queue", "session store", "Redis queue", "BullMQ", "Upstash", or "cache invalidation".
---

# Redis

## What This Does

Implement Redis for caching, session management, pub/sub messaging, job queues, rate limiting, and distributed locks. Covers key design, TTL strategy, cache invalidation patterns, and common data structure recipes.

## Non-Negotiables

- **Every key has a TTL.** Keys without expiry are a memory leak. The only exception is permanent reference data that is actively managed.
- **Namespace all keys.** Use `app:domain:entity:id` pattern. Never use bare strings like `"user"` or `"session"`.
- **Cache-aside by default.** Application reads cache first, falls back to database, populates cache on miss.
- **Never cache sensitive PII** (passwords, payment details, government IDs) — even with short TTLs.
- **Graceful degradation.** A Redis outage must never crash the application. Wrap all Redis calls in try/catch with database fallback.

## Setup

```bash
# Install client
npm install ioredis          # Node.js (preferred)
pip install redis            # Python

# Docker for local dev
docker run -d --name redis -p 6379:6379 redis:7-alpine

# Upstash (serverless Redis for edge/Vercel/Cloudflare)
npm install @upstash/redis
```

### Client Initialization (Node.js / ioredis)

```typescript
// lib/redis.ts
import Redis from 'ioredis'

const redis = new Redis({
  host: process.env.REDIS_HOST ?? 'localhost',
  port: Number(process.env.REDIS_PORT ?? 6379),
  password: process.env.REDIS_PASSWORD,
  tls: process.env.NODE_ENV === 'production' ? {} : undefined,
  maxRetriesPerRequest: 3,
  enableReadyCheck: true,
  lazyConnect: true,
})

redis.on('error', (err) => {
  console.error('[Redis] Connection error:', err.message)
  // Do NOT crash — app continues with DB fallback
})

export default redis
```

## Key Naming Convention

```
{app}:{domain}:{entity}:{id}:{variant}

Examples:
  myapp:user:profile:uuid-123
  myapp:post:list:published:page-1
  myapp:session:token:abc123
  myapp:ratelimit:api:ip:192.168.1.1
  myapp:lock:order-processor:uuid-456
```

## Caching Patterns

### Cache-Aside (Read-Through)

```typescript
async function getUserProfile(userId: string) {
  const key = `myapp:user:profile:${userId}`

  try {
    const cached = await redis.get(key)
    if (cached) return JSON.parse(cached)
  } catch (err) {
    console.warn('[Redis] Cache read failed, falling back to DB:', err.message)
  }

  // Cache miss — fetch from DB
  const user = await db.users.findById(userId)
  if (!user) return null

  // Populate cache (TTL: 15 minutes)
  try {
    await redis.setex(key, 900, JSON.stringify(user))
  } catch (err) {
    console.warn('[Redis] Cache write failed:', err.message)
  }

  return user
}
```

### Cache Invalidation

```typescript
// Invalidate on mutation
async function updateUserProfile(userId: string, data: Partial<User>) {
  await db.users.update({ where: { id: userId }, data })
  
  // Delete cache — it will be regenerated on next read
  await redis.del(`myapp:user:profile:${userId}`)
}

// Invalidate by pattern (use sparingly — expensive on large key sets)
async function invalidateUserCache(userId: string) {
  const keys = await redis.keys(`myapp:user:*:${userId}:*`)
  if (keys.length > 0) await redis.del(...keys)
}
```

### Cached List with Pagination

```typescript
async function getPublishedPosts(page: number) {
  const key = `myapp:post:list:published:page-${page}`
  
  const cached = await redis.get(key)
  if (cached) return JSON.parse(cached)

  const posts = await db.posts.findMany({
    where: { published: true },
    skip:  (page - 1) * 20,
    take:  20,
    orderBy: { created_at: 'desc' }
  })

  await redis.setex(key, 300, JSON.stringify(posts))  // 5-minute TTL
  return posts
}
```

## Rate Limiting

```typescript
// Sliding window rate limiter using sorted sets
async function rateLimitCheck(identifier: string, limit = 100, windowMs = 60_000) {
  const key    = `myapp:ratelimit:${identifier}`
  const now    = Date.now()
  const window = now - windowMs

  const pipe = redis.pipeline()
  pipe.zremrangebyscore(key, 0, window)         // remove old entries
  pipe.zadd(key, now, `${now}`)                 // add current request
  pipe.zcard(key)                               // count in window
  pipe.pexpire(key, windowMs)                   // set TTL

  const results = await pipe.exec()
  const count   = results?.[2]?.[1] as number

  return {
    allowed:   count <= limit,
    remaining: Math.max(0, limit - count),
    resetAt:   new Date(now + windowMs)
  }
}
```

## Pub/Sub

```typescript
// Publisher (any service)
async function publishEvent(channel: string, payload: object) {
  await redis.publish(channel, JSON.stringify(payload))
}

await publishEvent('myapp:orders:created', { orderId: 'uuid-123', userId: 'uuid-456' })

// Subscriber (dedicated connection — cannot use same client as pub)
const subscriber = redis.duplicate()

await subscriber.subscribe('myapp:orders:created')

subscriber.on('message', (channel, message) => {
  const payload = JSON.parse(message)
  console.log(`[${channel}]`, payload)
  // handle event
})
```

## Session Storage

```typescript
// Store session
async function createSession(sessionId: string, userId: string) {
  const key  = `myapp:session:${sessionId}`
  const data = { userId, createdAt: Date.now() }
  await redis.setex(key, 86400, JSON.stringify(data))  // 24h TTL
}

// Read session
async function getSession(sessionId: string) {
  const raw = await redis.get(`myapp:session:${sessionId}`)
  return raw ? JSON.parse(raw) : null
}

// Extend session on activity
async function touchSession(sessionId: string) {
  await redis.expire(`myapp:session:${sessionId}`, 86400)
}

// Destroy session (logout)
async function destroySession(sessionId: string) {
  await redis.del(`myapp:session:${sessionId}`)
}
```

## Distributed Lock

```typescript
// Prevent duplicate processing (e.g., webhook idempotency)
async function acquireLock(resource: string, ttlMs = 5000): Promise<string | null> {
  const key   = `myapp:lock:${resource}`
  const token = crypto.randomUUID()

  // SET NX: only set if key doesn't exist
  const result = await redis.set(key, token, 'PX', ttlMs, 'NX')
  return result === 'OK' ? token : null
}

async function releaseLock(resource: string, token: string) {
  const key    = `myapp:lock:${resource}`
  const script = `
    if redis.call("get", KEYS[1]) == ARGV[1] then
      return redis.call("del", KEYS[1])
    else return 0 end
  `
  await redis.eval(script, 1, key, token)
}
```

## TTL Reference Guide

| Data Type | Recommended TTL |
|-----------|----------------|
| User profile | 15 minutes |
| Public list page | 5 minutes |
| Search results | 60 seconds |
| API rate limit window | Window duration |
| Session | 24 hours (sliding) |
| Distributed lock | 5–30 seconds |
| Heavy computation (reports) | 1–6 hours |
| Static reference data | 24 hours |

## Commands Cheatsheet

```bash
# Inspect
redis-cli keys "myapp:*"
redis-cli ttl "myapp:user:profile:uuid-123"
redis-cli object encoding "myapp:key"

# Memory analysis
redis-cli memory usage "myapp:key"
redis-cli --bigkeys

# Monitor (dev only — never in prod)
redis-cli monitor
```
