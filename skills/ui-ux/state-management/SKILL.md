---
name: state-management
description: Implement client-side state management in React apps. Use when a user says "state management", "global state", "Zustand", "Redux", "Jotai", "React context", "TanStack Query", "client vs server state", "prop drilling", or "when to use context".
---

# State Management

## What This Does

Choose and implement the right state management approach for React apps. Covers the spectrum from local state → Context → Zustand → TanStack Query.

## State Type Decision Tree

```
Server/async data  → TanStack Query (React Query)
Global UI state    → Zustand
Form state         → React Hook Form
Component state    → useState / useReducer
URL state          → URLSearchParams / router
Shared UI (small)  → React Context
```

**The #1 anti-pattern:** putting server data in Zustand or Redux. Server state always lives in TanStack Query.

## Non-Negotiables

- **Collocate state as low as possible.** Only lift when two components genuinely share it.
- **Never store server data in Zustand.** Server data = TanStack Query.
- **Derive, don't duplicate.** Compute values from state, don't store them twice.
- **URL state for shareable views.** Filters, search, tabs belong in the URL.

## TanStack Query (Server State)

```bash
npm install @tanstack/react-query
```

### Setup

```typescript
// app/providers.tsx
'use client'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function QueryProvider({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000,
        retry: 1,
        refetchOnWindowFocus: false,
      },
    },
  }))
  return <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
}
```

### useQuery

```typescript
export const userKeys = {
  detail: (id: string) => ['users', id] as const,
}

export function useUser(userId: string) {
  return useQuery({
    queryKey: userKeys.detail(userId),
    queryFn:  () => fetch(`/api/users/${userId}`).then(r => r.json()),
    enabled:  !!userId,
  })
}

function UserCard({ userId }: { userId: string }) {
  const { data: user, isLoading, error } = useUser(userId)
  if (isLoading) return <Skeleton />
  if (error)     return <ErrorState error={error} />
  return <h2>{user.name}</h2>
}
```

### useMutation with Optimistic Update

```typescript
export function useUpdateUser(userId: string) {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: (data: Partial<User>) =>
      fetch(`/api/users/${userId}`, {
        method: 'PATCH',
        body: JSON.stringify(data),
        headers: { 'Content-Type': 'application/json' },
      }).then(r => r.json()),

    onMutate: async (newData) => {
      await queryClient.cancelQueries({ queryKey: userKeys.detail(userId) })
      const previous = queryClient.getQueryData<User>(userKeys.detail(userId))
      queryClient.setQueryData(userKeys.detail(userId), (old: User) => ({ ...old, ...newData }))
      return { previous }
    },

    onError: (_, __, context) => {
      queryClient.setQueryData(userKeys.detail(userId), context?.previous)
    },

    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: userKeys.detail(userId) })
    },
  })
}
```

## Zustand (Global Client State)

```bash
npm install zustand
```

### Auth Store

```typescript
// stores/authStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface AuthState {
  user:  User | null
  token: string | null
  login:  (user: User, token: string) => void
  logout: () => void
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user:   null,
      token:  null,
      login:  (user, token) => set({ user, token }),
      logout: () => set({ user: null, token: null }),
    }),
    { name: 'auth-store' }
  )
)

// Selective subscription — prevents re-renders on unrelated changes
const user = useAuthStore((state) => state.user)
```

### UI Store

```typescript
interface UIState {
  sidebarOpen: boolean
  activeModal: string | null
  toggleSidebar: () => void
  openModal:  (id: string) => void
  closeModal: () => void
}

export const useUIStore = create<UIState>()((set) => ({
  sidebarOpen: false,
  activeModal: null,
  toggleSidebar: () => set((s) => ({ sidebarOpen: !s.sidebarOpen })),
  openModal:  (id) => set({ activeModal: id }),
  closeModal: ()   => set({ activeModal: null }),
}))
```

## URL State (Shareable Views)

```typescript
// Use for: filters, search, page, tabs
import { useRouter, useSearchParams, usePathname } from 'next/navigation'

export function useSearchFilters() {
  const router       = useRouter()
  const pathname     = usePathname()
  const searchParams = useSearchParams()

  const setFilter = (key: string, value: string | null) => {
    const params = new URLSearchParams(searchParams.toString())
    if (value) params.set(key, value)
    else       params.delete(key)
    router.push(`${pathname}?${params.toString()}`)
  }

  return {
    query:    searchParams.get('q') ?? '',
    category: searchParams.get('category') ?? 'all',
    page:     Number(searchParams.get('page') ?? '1'),
    setFilter,
  }
}
```

## React Context (Lightweight Only)

```typescript
// Use ONLY for: theme, locale (read-heavy, write-rare)
const ThemeContext = createContext<ThemeContextValue | null>(null)

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light')
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme: () => setTheme(t => t === 'light' ? 'dark' : 'light') }}>
      {children}
    </ThemeContext.Provider>
  )
}

export function useTheme() {
  const ctx = useContext(ThemeContext)
  if (!ctx) throw new Error('useTheme must be used inside ThemeProvider')
  return ctx
}
```

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| Server data in Zustand | TanStack Query |
| One massive global store | Small focused stores per domain |
| Context for fast-updating state | Zustand |
| Filters in useState | URLSearchParams |
| Storing derived values | Compute on the fly |
