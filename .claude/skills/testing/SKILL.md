---
name: testing
description: |
  Testing patterns for our stack. Covers unit tests with Vitest, React component testing,
  Supabase test data, and E2E basics. Use when: setting up tests, writing test cases,
  or creating test data for development.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
---

# Testing

Testing patterns for Supabase + Next.js projects.

## Stack

| Type | Tool | Why |
|------|------|-----|
| Unit/Integration | Vitest | Fast, ESM native, works with Next.js |
| Component | React Testing Library | User-centric testing |
| E2E | Playwright | Cross-browser, reliable |
| Supabase | Local instance | Isolated test data |

## Setup

### 1. Install Dependencies

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### 2. Vitest Config

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./test/setup.ts'],
    include: ['**/*.test.{ts,tsx}'],
  },
})
```

### 3. Test Setup

```typescript
// test/setup.ts
import '@testing-library/jest-dom'
import { vi } from 'vitest'

// Mock Supabase client
vi.mock('@/lib/supabase', () => ({
  supabase: {
    from: vi.fn(() => ({
      select: vi.fn().mockReturnThis(),
      insert: vi.fn().mockReturnThis(),
      update: vi.fn().mockReturnThis(),
      delete: vi.fn().mockReturnThis(),
      eq: vi.fn().mockReturnThis(),
      single: vi.fn(),
    })),
    auth: {
      getUser: vi.fn(),
      signInWithPassword: vi.fn(),
      signOut: vi.fn(),
    },
  },
}))
```

## Patterns

### Unit Test - Utility Function

```typescript
// utils/format.test.ts
import { describe, it, expect } from 'vitest'
import { formatCurrency } from './format'

describe('formatCurrency', () => {
  it('formats SEK correctly', () => {
    expect(formatCurrency(1000, 'SEK')).toBe('1 000 kr')
  })

  it('handles zero', () => {
    expect(formatCurrency(0, 'SEK')).toBe('0 kr')
  })
})
```

### Component Test - With Mocked Data

```typescript
// components/UserList.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import { UserList } from './UserList'

const mockUsers = [
  { id: '1', name: 'Alice', email: 'alice@test.com' },
  { id: '2', name: 'Bob', email: 'bob@test.com' },
]

describe('UserList', () => {
  it('renders users', () => {
    render(<UserList users={mockUsers} />)

    expect(screen.getByText('Alice')).toBeInTheDocument()
    expect(screen.getByText('Bob')).toBeInTheDocument()
  })

  it('shows empty state when no users', () => {
    render(<UserList users={[]} />)

    expect(screen.getByText(/no users/i)).toBeInTheDocument()
  })
})
```

### Component Test - With User Interaction

```typescript
// components/LoginForm.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { LoginForm } from './LoginForm'

describe('LoginForm', () => {
  it('calls onSubmit with credentials', async () => {
    const onSubmit = vi.fn()
    const user = userEvent.setup()

    render(<LoginForm onSubmit={onSubmit} />)

    await user.type(screen.getByLabelText(/email/i), 'test@example.com')
    await user.type(screen.getByLabelText(/password/i), 'password123')
    await user.click(screen.getByRole('button', { name: /sign in/i }))

    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    })
  })

  it('shows validation error for invalid email', async () => {
    const user = userEvent.setup()

    render(<LoginForm onSubmit={vi.fn()} />)

    await user.type(screen.getByLabelText(/email/i), 'invalid')
    await user.click(screen.getByRole('button', { name: /sign in/i }))

    expect(screen.getByText(/valid email/i)).toBeInTheDocument()
  })
})
```

### Supabase Integration Test

```typescript
// services/users.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { supabase } from '@/lib/supabase'
import { getUsers, createUser } from './users'

vi.mock('@/lib/supabase')

describe('User Service', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('fetches users from Supabase', async () => {
    const mockData = [{ id: '1', name: 'Alice' }]

    vi.mocked(supabase.from).mockReturnValue({
      select: vi.fn().mockResolvedValue({ data: mockData, error: null }),
    } as any)

    const users = await getUsers()

    expect(supabase.from).toHaveBeenCalledWith('users')
    expect(users).toEqual(mockData)
  })

  it('throws on Supabase error', async () => {
    vi.mocked(supabase.from).mockReturnValue({
      select: vi.fn().mockResolvedValue({
        data: null,
        error: { message: 'Connection failed' }
      }),
    } as any)

    await expect(getUsers()).rejects.toThrow('Connection failed')
  })
})
```

## Supabase Test Data

### Option 1: Seed Script (for development)

```typescript
// scripts/seed.ts
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_KEY! // Service key for seeding
)

async function seed() {
  // Clear existing test data
  await supabase.from('users').delete().like('email', '%@test.com')

  // Insert test users
  await supabase.from('users').insert([
    { email: 'admin@test.com', role: 'admin', name: 'Test Admin' },
    { email: 'user@test.com', role: 'user', name: 'Test User' },
  ])

  console.log('Seeded test data')
}

seed()
```

### Option 2: Factory Functions (for tests)

```typescript
// test/factories.ts
import { faker } from '@faker-js/faker'

export function createUser(overrides = {}) {
  return {
    id: faker.string.uuid(),
    email: faker.internet.email(),
    name: faker.person.fullName(),
    created_at: faker.date.past().toISOString(),
    ...overrides,
  }
}

export function createProject(overrides = {}) {
  return {
    id: faker.string.uuid(),
    name: faker.company.name(),
    status: 'active',
    created_at: faker.date.past().toISOString(),
    ...overrides,
  }
}

// Usage in tests:
// const user = createUser({ role: 'admin' })
```

## E2E with Playwright (basics)

### Setup

```bash
npm init playwright@latest
```

### Simple E2E Test

```typescript
// e2e/login.spec.ts
import { test, expect } from '@playwright/test'

test('user can log in', async ({ page }) => {
  await page.goto('/login')

  await page.fill('[name="email"]', 'test@example.com')
  await page.fill('[name="password"]', 'password123')
  await page.click('button[type="submit"]')

  await expect(page).toHaveURL('/dashboard')
  await expect(page.locator('h1')).toContainText('Dashboard')
})
```

## Running Tests

```bash
# Run all tests
npm test

# Watch mode
npm test -- --watch

# Coverage
npm test -- --coverage

# Specific file
npm test -- users.test.ts
```

## Package.json Scripts

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "playwright test",
    "seed": "npx tsx scripts/seed.ts"
  }
}
```

## Test Checklist

Before shipping:

- [ ] Critical paths have tests (login, main features)
- [ ] Edge cases covered (empty states, errors)
- [ ] No console errors in test output
- [ ] Tests run in CI

## Rules

1. **Test behavior, not implementation** - What does the user see?
2. **One assertion focus per test** - Keep tests focused
3. **Mock external services** - Supabase, APIs
4. **Use factories for test data** - Consistent, maintainable
5. **Don't test library code** - Trust React, Supabase, etc.
