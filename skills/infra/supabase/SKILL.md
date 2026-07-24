---
name: supabase
description: Build backends with Supabase (PostgreSQL, Auth, Storage, Realtime, Edge Functions). Use when a user says "use Supabase", "Supabase auth", "Supabase database", "Supabase storage", "Supabase realtime", "row-level security", "RLS policy", "Supabase edge function", "supabase-js", or "connect my app to Supabase".
---

# Supabase

## What This Does

Implement full-stack backends using Supabase: PostgreSQL database with row-level security, authentication flows, file storage, realtime subscriptions, and serverless edge functions. Covers client setup, schema design, RLS policies, and production best practices.

## Non-Negotiables

- **Always enable RLS** on every table. A table without RLS is public by default — this is a critical security gap.
- **Never expose `service_role` key** on the client. Use only `anon` key in browser code.
- **Type-safe client.** Always generate and use TypeScript types from the Supabase CLI.
- **Migrations over direct edits.** All schema changes via `supabase migration new`, never via Studio on prod.
- **Realtime subscriptions must be cleaned up** on component unmount to prevent memory leaks.

## Setup

```bash
# Install Supabase CLI
npm install -g supabase

# Initialize in project
supabase init

# Start local dev stack (PostgreSQL + Auth + Storage + Studio)
supabase start

# Link to remote project
supabase link --project-ref <project-ref>

# Install client SDK
npm install @supabase/supabase-js
```

### Client Initialization

```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js'
import type { Database } from './database.types'   // generated types

const supabaseUrl  = process.env.NEXT_PUBLIC_SUPABASE_URL!
const supabaseAnon = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!

export const supabase = createClient<Database>(supabaseUrl, supabaseAnon)
```

### Generate TypeScript Types

```bash
supabase gen types typescript --linked > lib/database.types.ts
```

## Database (PostgreSQL)

### Schema Design Pattern

```sql
-- Always use UUID primary keys
-- Always include created_at / updated_at
-- Always enable RLS immediately after CREATE TABLE

create table public.posts (
  id          uuid primary key default gen_random_uuid(),
  user_id     uuid not null references auth.users(id) on delete cascade,
  title       text not null check (char_length(title) between 1 and 200),
  body        text,
  published   boolean not null default false,
  created_at  timestamptz not null default now(),
  updated_at  timestamptz not null default now()
);

-- Enable RLS immediately
alter table public.posts enable row level security;

-- Auto-update updated_at
create trigger set_updated_at
  before update on public.posts
  for each row execute function moddatetime(updated_at);
```

### Row-Level Security (RLS) Policies

```sql
-- Allow users to read their own posts + all published posts
create policy "Users read own or published posts"
  on public.posts for select
  using (auth.uid() = user_id or published = true);

-- Only the owner can insert posts
create policy "Users insert own posts"
  on public.posts for insert
  with check (auth.uid() = user_id);

-- Only the owner can update/delete
create policy "Users update own posts"
  on public.posts for update
  using (auth.uid() = user_id);

create policy "Users delete own posts"
  on public.posts for delete
  using (auth.uid() = user_id);
```

### Querying from Client

```typescript
// SELECT with filters
const { data, error } = await supabase
  .from('posts')
  .select('id, title, body, created_at, user:user_id(email)')
  .eq('published', true)
  .order('created_at', { ascending: false })
  .limit(20)

// INSERT
const { data, error } = await supabase
  .from('posts')
  .insert({ title, body, user_id: session.user.id })
  .select()
  .single()

// UPDATE
const { error } = await supabase
  .from('posts')
  .update({ title, updated_at: new Date().toISOString() })
  .eq('id', postId)
  .eq('user_id', session.user.id)   // redundant safety check + satisfied by RLS

// DELETE
const { error } = await supabase
  .from('posts')
  .delete()
  .eq('id', postId)
```

## Authentication

```typescript
// Email + Password signup
const { data, error } = await supabase.auth.signUp({
  email,
  password,
  options: { data: { display_name: name } }  // stored in auth.users.raw_user_meta_data
})

// Sign in
const { data, error } = await supabase.auth.signInWithPassword({ email, password })

// OAuth (Google, GitHub, etc.)
await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: { redirectTo: `${window.location.origin}/auth/callback` }
})

// Listen to auth state
supabase.auth.onAuthStateChange((event, session) => {
  if (event === 'SIGNED_IN')  setUser(session?.user ?? null)
  if (event === 'SIGNED_OUT') setUser(null)
})

// Get current session
const { data: { session } } = await supabase.auth.getSession()
```

## Storage

```typescript
// Upload a file
const { data, error } = await supabase.storage
  .from('avatars')
  .upload(`${userId}/avatar.png`, file, {
    cacheControl: '3600',
    upsert: true,
    contentType: 'image/png'
  })

// Get public URL
const { data } = supabase.storage
  .from('avatars')
  .getPublicUrl(`${userId}/avatar.png`)

// Delete
await supabase.storage.from('avatars').remove([`${userId}/avatar.png`])
```

### Storage RLS (set in Supabase Studio → Storage → Policies)

```sql
-- Allow authenticated users to upload to their own folder
create policy "Users upload to own folder"
  on storage.objects for insert to authenticated
  with check (bucket_id = 'avatars' and (storage.foldername(name))[1] = auth.uid()::text);
```

## Realtime

```typescript
// Subscribe to table changes
const channel = supabase
  .channel('posts-changes')
  .on('postgres_changes', {
    event: '*',
    schema: 'public',
    table: 'posts',
    filter: `user_id=eq.${userId}`
  }, (payload) => {
    console.log('Change received:', payload)
    // handle INSERT / UPDATE / DELETE
  })
  .subscribe()

// CRITICAL: Clean up on unmount
return () => { supabase.removeChannel(channel) }
```

## Edge Functions

```typescript
// supabase/functions/send-email/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

serve(async (req) => {
  const { to, subject, body } = await req.json()

  // Verify JWT from Authorization header
  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_ANON_KEY')!,
    { global: { headers: { Authorization: req.headers.get('Authorization')! } } }
  )

  const { data: { user }, error } = await supabase.auth.getUser()
  if (error || !user) return new Response('Unauthorized', { status: 401 })

  // ... send email logic

  return new Response(JSON.stringify({ success: true }), {
    headers: { 'Content-Type': 'application/json' }
  })
})
```

```bash
# Deploy edge function
supabase functions deploy send-email
```

## Migration Workflow

```bash
# Create a new migration
supabase migration new add_posts_table

# Apply locally
supabase db reset

# Push to remote
supabase db push

# Pull remote schema changes
supabase db pull
```

## Security Checklist

- [ ] RLS enabled on ALL tables
- [ ] No `service_role` key in client-side code
- [ ] TypeScript types generated and used
- [ ] Storage bucket policies configured
- [ ] Email confirmation enabled for auth
- [ ] Edge functions validate JWT before processing
- [ ] `updated_at` triggers in place
- [ ] Indexes on foreign keys and frequently queried columns
