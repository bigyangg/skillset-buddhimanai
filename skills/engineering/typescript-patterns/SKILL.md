---
name: typescript-patterns
description: Apply advanced TypeScript patterns to make code safer and more maintainable. Use when a user says "TypeScript types", "generic types", "utility types", "discriminated union", "type narrowing", "conditional types", "mapped types", "infer keyword", "type guard", "TypeScript patterns", "strict TypeScript", or "TypeScript best practices".
---

# TypeScript Patterns

## What This Does

Apply advanced TypeScript type system patterns to eliminate runtime errors, improve DX, and make large codebases maintainable. Covers utility types, generics, discriminated unions, type narrowing, branded types, and patterns that catch real bugs at compile time.

## Non-Negotiables

- **`strict: true` always.** No exceptions. `noImplicitAny`, `strictNullChecks`, and `strictFunctionTypes` catch entire classes of bugs.
- **Never use `any`.** If you're reaching for `any`, use `unknown` and narrow it explicitly.
- **Type at the boundary, infer inward.** Type API responses, user inputs, env vars — let TypeScript infer everything inside.
- **Discriminated unions over booleans.** `{ status: 'loading' } | { status: 'error', error: Error } | { status: 'success', data: T }` is far safer than `isLoading: boolean, error?: Error, data?: T`.
- **No type assertions (`as X`) in business logic.** Assertions hide bugs. Use type guards instead.

## Core Patterns

### 1. Discriminated Unions (Async State)

```typescript
// ❌ Bad: multiple booleans create impossible states
type BadState = {
  isLoading: boolean
  error?: Error
  data?: User
}
// isLoading=true AND data=User is technically possible — it shouldn't be

// ✅ Good: discriminated union makes impossible states impossible
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'error';   error: Error }
  | { status: 'success'; data: T }

function render(state: AsyncState<User>) {
  switch (state.status) {
    case 'idle':    return <IdleView />
    case 'loading': return <Spinner />
    case 'error':   return <ErrorView error={state.error} />   // TS knows error exists here
    case 'success': return <UserCard user={state.data} />       // TS knows data exists here
  }
}
```

### 2. Branded/Nominal Types

```typescript
// Prevent passing wrong IDs to functions (string is too broad)

type UserId   = string & { readonly __brand: 'UserId' }
type OrderId  = string & { readonly __brand: 'OrderId' }

function createUserId(id: string): UserId   { return id as UserId }
function createOrderId(id: string): OrderId { return id as OrderId }

function getUser(id: UserId)  { /* ... */ }
function getOrder(id: OrderId) { /* ... */ }

const userId  = createUserId('user-123')
const orderId = createOrderId('order-456')

getUser(userId)    // ✅
getUser(orderId)   // ❌ TS Error: Argument of type 'OrderId' is not assignable to parameter of type 'UserId'
```

### 3. Type Guards

```typescript
// User-defined type guards for runtime narrowing
interface Cat   { kind: 'cat';  meow(): void }
interface Dog   { kind: 'dog';  bark(): void }
type Animal = Cat | Dog

// Type guard function
function isCat(animal: Animal): animal is Cat {
  return animal.kind === 'cat'
}

function makeSound(animal: Animal) {
  if (isCat(animal)) {
    animal.meow()   // TS knows it's Cat here
  } else {
    animal.bark()   // TS knows it's Dog here
  }
}

// Assertion function (throws if check fails)
function assertIsDefined<T>(val: T): asserts val is NonNullable<T> {
  if (val == null) throw new Error(`Expected defined value, got ${val}`)
}
```

### 4. Generics with Constraints

```typescript
// Generic repository pattern
interface Entity {
  id:         string
  created_at: Date
  updated_at: Date
}

class Repository<T extends Entity> {
  constructor(private tableName: string) {}

  async findById(id: string): Promise<T | null> {
    // DB query — returns typed T
    return db.query<T>(`SELECT * FROM ${this.tableName} WHERE id = $1`, [id])
  }

  async findMany(filters: Partial<T>): Promise<T[]> {
    // ...
  }

  async save(entity: Omit<T, 'id' | 'created_at' | 'updated_at'>): Promise<T> {
    // ...
  }
}

// Usage
const userRepo  = new Repository<User>('users')
const orderRepo = new Repository<Order>('orders')
```

### 5. Utility Types (Standard Library)

```typescript
interface User {
  id:        string
  email:     string
  name:      string
  password:  string
  role:      'admin' | 'user'
  createdAt: Date
}

type UserPreview  = Pick<User, 'id' | 'name' | 'role'>       // only selected fields
type UserUpdate   = Partial<Pick<User, 'name' | 'email'>>     // partial subset
type PublicUser   = Omit<User, 'password'>                    // exclude sensitive
type ReadonlyUser = Readonly<User>                            // immutable
type CreateUser   = Omit<User, 'id' | 'createdAt'>           // input type

// Record for dictionaries
type UserMap = Record<string, User>
const users: UserMap = {}

// ReturnType and Parameters
async function getUser(id: string): Promise<User> { /* ... */ }
type GetUserReturn = Awaited<ReturnType<typeof getUser>>  // User
type GetUserArgs   = Parameters<typeof getUser>            // [string]
```

### 6. Mapped Types

```typescript
// Build variant types programmatically
type Nullable<T> = { [K in keyof T]: T[K] | null }
type Optional<T> = { [K in keyof T]?: T[K] }

// Selectively make some fields required
type RequireFields<T, K extends keyof T> = T & Required<Pick<T, K>>

// Form state for any model
type FormState<T> = {
  [K in keyof T]: {
    value:   T[K]
    error:   string | null
    touched: boolean
  }
}
```

### 7. Conditional Types

```typescript
// Extract array element type
type UnwrapArray<T> = T extends (infer Item)[] ? Item : T

type StringArray = string[]
type StringItem  = UnwrapArray<StringArray>   // string

// Promise unwrapping
type Awaited<T> = T extends Promise<infer R> ? Awaited<R> : T

// Make specific keys required, others optional
type WithRequired<T, K extends keyof T> = Pick<Required<T>, K> & Omit<T, K>

// Exclude null/undefined
type NonNullable<T> = T extends null | undefined ? never : T
```

### 8. `satisfies` Operator (TypeScript 4.9+)

```typescript
// Validate type without widening
const config = {
  db_url:   'postgresql://localhost/dev',
  port:     3000,
  debug:    true,
} satisfies Record<string, string | number | boolean>

// config.db_url is still `string` literal type, not widened
// config.port is still `number`, not `string | number | boolean`
```

### 9. Template Literal Types

```typescript
type EventName  = 'user' | 'order' | 'payment'
type EventKind  = 'created' | 'updated' | 'deleted'
type FullEvent  = `${EventName}:${EventKind}`
// 'user:created' | 'user:updated' | 'user:deleted' | 'order:created' | ...

type CSSProperty = `${string}-${string}`  // 'background-color', 'font-size', etc.

// Extract from template
type ExtractId<T extends string> = T extends `${infer _prefix}-${infer Id}` ? Id : never
type OrderNumber = ExtractId<'order-12345'>   // '12345'
```

## `tsconfig.json` (Strict Baseline)

```json
{
  "compilerOptions": {
    "target":              "ES2022",
    "lib":                 ["ES2022", "DOM"],
    "module":              "NodeNext",
    "moduleResolution":    "NodeNext",
    "strict":              true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns":   true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck":        true
  }
}
```

## Anti-Pattern Checklist

| Anti-Pattern | Fix |
|-------------|-----|
| `as any` | Use `unknown` + type guard or generics |
| `// @ts-ignore` | Fix the underlying type error |
| `isLoading + error + data` flags | Discriminated union |
| `string` for IDs | Branded types |
| `object` type | `Record<string, unknown>` or specific interface |
| Non-null assertion `!` in business logic | Optional chaining + null check |
| `Function` type | Specific `(arg: T) => R` signature |
