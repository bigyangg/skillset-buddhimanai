---
name: postgres
description: Design, query, and optimize PostgreSQL databases. Use when a user says "design a database schema", "optimize a slow query", "add an index", "write a migration", "PostgreSQL", "SQL query", "explain analyze", "full-text search", "JSONB column", "database normalization", "upsert", or "database performance".
---

# PostgreSQL

## What This Does

Design production-grade PostgreSQL schemas, write efficient queries, optimize performance with indexes and EXPLAIN ANALYZE, and author safe migrations. Covers normalization, JSONB patterns, full-text search, CTEs, and common query anti-patterns.

## Non-Negotiables

- **Always use migrations.** Never alter a production schema by hand — every change is a versioned SQL file.
- **UUID or BIGSERIAL primary keys.** Use `gen_random_uuid()` (UUID v4) for distributed systems; `BIGSERIAL` for high-insert tables where sortability matters.
- **Timestamps on every table.** `created_at` and `updated_at` are mandatory.
- **Index foreign keys.** PostgreSQL does not auto-index foreign keys. Missing FK indexes cause sequential scans on JOINs.
- **`EXPLAIN ANALYZE` before shipping.** Every non-trivial query must be analyzed.
- **Transactions for multi-statement writes.** Never run multiple related `INSERT/UPDATE/DELETE` statements outside a transaction.
- **Never `SELECT *` in application code.** Always name columns explicitly.

## Schema Design Patterns

### Standard Table Template

```sql
create table public.orders (
  id           uuid         primary key default gen_random_uuid(),
  user_id      uuid         not null references public.users(id) on delete cascade,
  status       text         not null default 'pending'
                            check (status in ('pending','processing','shipped','delivered','cancelled')),
  total_cents  integer      not null check (total_cents >= 0),
  metadata     jsonb        not null default '{}',
  created_at   timestamptz  not null default now(),
  updated_at   timestamptz  not null default now()
);

-- Index the FK (mandatory)
create index idx_orders_user_id on public.orders(user_id);

-- Index frequently filtered/sorted columns
create index idx_orders_status_created on public.orders(status, created_at desc);

-- Partial index for active orders (common optimization)
create index idx_orders_active on public.orders(user_id, created_at desc)
  where status not in ('delivered', 'cancelled');
```

### Normalization Guidelines

| Form | When to Use |
|------|------------|
| 1NF | Always — eliminate repeating groups, use separate rows |
| 2NF | When composite PKs exist — every column depends on **full** key |
| 3NF | Standard — no transitive dependencies |
| Denormalize | Only when proven by EXPLAIN ANALYZE + query frequency |

### JSONB Patterns

```sql
-- Store flexible attributes in JSONB
alter table products add column attributes jsonb not null default '{}';

-- Query JSONB
select * from products where attributes->>'color' = 'blue';
select * from products where attributes @> '{"brand": "Acme"}';

-- Index for fast JSONB lookups
create index idx_products_attributes_gin on products using gin(attributes);

-- Index a specific JSONB key
create index idx_products_color on products((attributes->>'color'));
```

## Query Patterns

### Pagination (Cursor-Based — Preferred for Large Tables)

```sql
-- Cursor pagination: faster than OFFSET for large result sets
select id, title, created_at
from posts
where (created_at, id) < ($last_created_at, $last_id)   -- cursor
  and published = true
order by created_at desc, id desc
limit 20;
```

### Upsert

```sql
insert into user_preferences (user_id, key, value)
values ($1, $2, $3)
on conflict (user_id, key)
do update set
  value      = excluded.value,
  updated_at = now();
```

### CTE (Common Table Expressions)

```sql
-- Readable chained queries using CTEs
with recent_orders as (
  select user_id, count(*) as order_count, sum(total_cents) as total_spend
  from orders
  where created_at > now() - interval '30 days'
    and status = 'delivered'
  group by user_id
),
ranked_users as (
  select
    u.id,
    u.email,
    ro.order_count,
    ro.total_spend,
    rank() over (order by ro.total_spend desc) as spend_rank
  from users u
  join recent_orders ro on ro.user_id = u.id
)
select * from ranked_users where spend_rank <= 100;
```

### Full-Text Search

```sql
-- Add a tsvector column for search
alter table articles add column search_vector tsvector
  generated always as (
    to_tsvector('english', coalesce(title, '') || ' ' || coalesce(body, ''))
  ) stored;

create index idx_articles_search on articles using gin(search_vector);

-- Query
select id, title, ts_rank(search_vector, query) as rank
from articles, to_tsquery('english', 'machine & learning') query
where search_vector @@ query
order by rank desc
limit 20;
```

## Performance: EXPLAIN ANALYZE

```sql
-- Always use EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
explain (analyze, buffers, format text)
select u.email, count(o.id)
from users u
left join orders o on o.user_id = u.id
where u.created_at > '2024-01-01'
group by u.email;
```

### Reading EXPLAIN Output

| Warning Sign | Fix |
|-------------|-----|
| `Seq Scan` on large table | Add an index on the filter column |
| `Hash Join` with large batches | Increase `work_mem`, add index |
| `Nested Loop` with high rows | Check FK index, consider explicit JOIN order |
| High `actual rows` vs `estimated rows` | Run `ANALYZE table_name` to update stats |
| `Filter: (rows removed by filter: N)` | Index is being scanned but filtering heavily — add partial index |

## Migrations (Best Practice)

```sql
-- migrations/0001_create_users.sql
begin;

create table public.users (
  id         uuid        primary key default gen_random_uuid(),
  email      text        not null unique,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

create index idx_users_email on public.users(email);

commit;
```

```bash
# Using Flyway
flyway migrate

# Using golang-migrate
migrate -database "postgres://..." -path ./migrations up

# Using Supabase CLI
supabase migration new create_users
supabase db push
```

## Maintenance Checklist

- [ ] All FK columns have indexes
- [ ] `VACUUM ANALYZE` scheduled (automatic in most managed PGs)
- [ ] `pg_stat_user_tables` monitored for bloat (`n_dead_tup`)
- [ ] Slow query log enabled (`log_min_duration_statement = 1000`)
- [ ] Connection pooling via PgBouncer or built-in (Supabase, RDS Proxy)
- [ ] Point-in-time recovery (PITR) enabled on production
- [ ] `statement_timeout` set to prevent runaway queries
- [ ] Indexes reviewed quarterly — drop unused ones (`pg_stat_user_indexes`)
