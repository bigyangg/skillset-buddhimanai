---
name: database-design
description: Design relational database schemas from scratch or improve existing ones. Use when a user says "design a database", "create a schema", "database modeling", "entity relationships", "normalize this table", "ER diagram", "schema review", "foreign keys", "many-to-many", "soft deletes", "multi-tenancy schema", or "database versioning".
---

# Database Design

## What This Does

Design normalized, production-ready relational database schemas from requirements. Covers entity modeling, relationship types, normalization to 3NF, common patterns (soft deletes, audit trails, multi-tenancy, hierarchies), and schema review methodology.

## Non-Negotiables

- **Name tables with plural nouns.** `users`, `orders`, `products` — not `user`, `tbl_users`, `UserData`.
- **Every table has a surrogate primary key.** Use UUID (distributed) or BIGSERIAL (sequential). Never use natural keys (email, SSN) as PKs.
- **`created_at` and `updated_at` on every table.** Non-negotiable for debugging and auditing.
- **Foreign keys must be declared.** Don't rely on application-level enforcement alone.
- **Index every foreign key.** PostgreSQL and MySQL do not auto-index FKs.
- **Never store derived data.** If a value can be computed from other columns, compute it at query time.

## Schema Design Workflow

### Step 1 — Identify Entities from Requirements

Parse requirements narrative for nouns (entities) and verbs (relationships):

```
"Users can create projects. Projects contain tasks. Tasks can be assigned to users.
 Users can comment on tasks. Projects belong to organizations."

Entities: User, Project, Task, Comment, Organization
Relationships:
- User creates Project (1:many)
- Project contains Task (1:many)  
- Task assigned to User (many:1)
- User comments on Task (1:many → Comments)
- Organization owns Projects (1:many)
- User belongs to Organization (many:many → Memberships)
```

### Step 2 — Relationship Types

| Type | Implemented As | Example |
|------|---------------|---------|
| One-to-Many (1:N) | FK on the "many" side | orders.user_id → users.id |
| Many-to-Many (M:N) | Junction table | project_members(project_id, user_id) |
| One-to-One (1:1) | FK + UNIQUE constraint | user_profiles.user_id UNIQUE → users.id |
| Self-referential | FK to same table | categories.parent_id → categories.id |

### Step 3 — Normalization Checklist

**1NF** (First Normal Form):
- [ ] Each column holds atomic (indivisible) values
- [ ] No repeating groups or arrays in columns
- [ ] Each row is uniquely identifiable (PK exists)

**2NF** (Second Normal Form):
- [ ] Already in 1NF
- [ ] Every non-key column fully depends on the entire PK (no partial dependencies)
- [ ] Violators: `order_items(order_id, product_id, product_name)` — `product_name` depends only on `product_id`

**3NF** (Third Normal Form):
- [ ] Already in 2NF
- [ ] No transitive dependencies (non-key col A → non-key col B)
- [ ] Violators: `orders(id, customer_id, customer_email)` — `customer_email` depends on `customer_id`, not on `order.id`

## Core Schema Patterns

### Standard Table + Key Indexes

```sql
create table public.tasks (
  id           uuid         primary key default gen_random_uuid(),
  project_id   uuid         not null references public.projects(id) on delete cascade,
  assignee_id  uuid         references public.users(id) on delete set null,
  title        text         not null check (char_length(title) between 1 and 255),
  description  text,
  status       text         not null default 'todo'
                            check (status in ('todo','in_progress','review','done','cancelled')),
  priority     smallint     not null default 2 check (priority between 1 and 5),
  due_date     date,
  created_at   timestamptz  not null default now(),
  updated_at   timestamptz  not null default now()
);

create index idx_tasks_project_id   on public.tasks(project_id);
create index idx_tasks_assignee_id  on public.tasks(assignee_id);
create index idx_tasks_status       on public.tasks(status, project_id);
create index idx_tasks_due_date     on public.tasks(due_date) where due_date is not null;
```

### Many-to-Many: Junction Table

```sql
-- Users can belong to many Organizations, Organizations have many Users
create table public.memberships (
  id              uuid         primary key default gen_random_uuid(),
  user_id         uuid         not null references public.users(id) on delete cascade,
  organization_id uuid         not null references public.organizations(id) on delete cascade,
  role            text         not null default 'member'
                               check (role in ('owner','admin','member','viewer')),
  joined_at       timestamptz  not null default now(),
  
  unique (user_id, organization_id)   -- prevents duplicate memberships
);

create index idx_memberships_user_id         on public.memberships(user_id);
create index idx_memberships_organization_id on public.memberships(organization_id);
```

### Soft Deletes

```sql
-- Add to any table that needs soft deletes
alter table public.tasks
  add column deleted_at timestamptz;

-- Partial index: only undeleted rows in normal queries
create index idx_tasks_active on public.tasks(project_id, created_at desc)
  where deleted_at is null;

-- View that hides deleted rows
create view public.active_tasks as
  select * from public.tasks where deleted_at is null;
```

### Audit Trail

```sql
-- Generic audit log for any table
create table public.audit_log (
  id          bigserial    primary key,
  table_name  text         not null,
  record_id   uuid         not null,
  action      text         not null check (action in ('INSERT','UPDATE','DELETE')),
  old_data    jsonb,
  new_data    jsonb,
  changed_by  uuid         references public.users(id) on delete set null,
  changed_at  timestamptz  not null default now()
);

create index idx_audit_log_record on public.audit_log(table_name, record_id);
create index idx_audit_log_user   on public.audit_log(changed_by, changed_at desc);

-- Trigger to auto-populate (PostgreSQL)
create or replace function audit_trigger_fn() returns trigger as $$
begin
  insert into public.audit_log(table_name, record_id, action, old_data, new_data)
  values (
    TG_TABLE_NAME,
    coalesce(NEW.id, OLD.id),
    TG_OP,
    case when TG_OP = 'DELETE' then to_jsonb(OLD) end,
    case when TG_OP in ('INSERT','UPDATE') then to_jsonb(NEW) end
  );
  return NEW;
end;
$$ language plpgsql;

create trigger audit_tasks
  after insert or update or delete on public.tasks
  for each row execute function audit_trigger_fn();
```

### Multi-Tenancy (Row-Level Isolation)

```sql
-- Schema-based approach: every entity references tenant
create table public.projects (
  id              uuid  primary key default gen_random_uuid(),
  organization_id uuid  not null references public.organizations(id) on delete cascade,
  name            text  not null,
  -- ... other columns
);

-- All queries filtered by org
create index idx_projects_org on public.projects(organization_id);

-- PostgreSQL RLS enforcement
alter table public.projects enable row level security;

create policy "orgs access own projects"
  on public.projects
  using (organization_id = current_setting('app.current_org_id')::uuid);
```

### Self-Referential (Tree / Hierarchy)

```sql
-- Category tree (adjacency list — simple)
create table public.categories (
  id         uuid  primary key default gen_random_uuid(),
  parent_id  uuid  references public.categories(id) on delete cascade,
  name       text  not null,
  slug       text  not null unique
);

create index idx_categories_parent_id on public.categories(parent_id);

-- Query full ancestry with recursive CTE
with recursive ancestry as (
  select id, parent_id, name, 0 as depth
  from categories where id = $target_id
  
  union all
  
  select c.id, c.parent_id, c.name, a.depth + 1
  from categories c
  join ancestry a on c.id = a.parent_id
)
select * from ancestry order by depth desc;
```

## Schema Review Checklist

- [ ] All tables have UUID/BIGSERIAL PK
- [ ] All tables have `created_at` and `updated_at`
- [ ] All FKs declared with appropriate `ON DELETE` action
- [ ] All FK columns have indexes
- [ ] `CHECK` constraints on all enum-like text columns
- [ ] No derived data stored in columns
- [ ] No arrays of primitive values (use 1:N table instead)
- [ ] Soft delete tables have partial indexes for active rows
- [ ] No PII directly in bulk-queryable columns without access controls
- [ ] Naming: plural tables, `snake_case`, no reserved words (`order` → `orders`)
