---
name: e2e-testing
description: Write end-to-end tests with Playwright or Cypress to verify user flows. Use when a user says "e2e tests", "end-to-end testing", "Playwright", "Cypress", "browser automation tests", "test user flow", "test login flow", "test checkout", "visual regression", "test my UI", or "integration tests from the browser".
---

# End-to-End Testing

## What This Does

Write, organize, and maintain production-quality end-to-end tests using Playwright (primary) and Cypress (secondary). Covers test strategy, page object model, authentication setup, fixtures, CI integration, and visual regression testing.

## Tool Choice

| Criterion | Playwright | Cypress |
|-----------|-----------|---------|
| Multi-browser | Chrome, Firefox, Safari, Edge | Chrome, Firefox, Edge (Safari limited) |
| Language | JS/TS/Python/Java | JS/TS only |
| Speed | Faster (parallel, no iframe) | Slower (same-origin only) |
| API testing | Built-in (`request` context) | Plugin required |
| CI setup | Single binary, easy | Cypress dashboard or docker |
| **Recommendation** | **Default choice** | Use if team already on Cypress |

## Non-Negotiables

- **Test user-visible behavior, not implementation.** Query by role, label, and text — not CSS selectors or test IDs.
- **Each test is independent.** Tests must not share state. Never rely on test ordering.
- **Authentication is setup, not a test.** Use `storageState` for Playwright or `cy.session` for Cypress — don't log in via UI in every test.
- **No hard-coded `wait(1000)`.** Use `waitFor` and auto-waiting assertions instead.
- **Tests run against a real environment** (local or staging). E2E tests on mocked APIs are integration tests in disguise.

## Setup (Playwright)

```bash
npm init playwright@latest
```

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir:            './e2e',
  timeout:            30_000,
  retries:            process.env.CI ? 2 : 0,
  workers:            process.env.CI ? 2 : undefined,
  reporter:           [['html'], ['list']],
  use: {
    baseURL:          'http://localhost:3000',
    trace:            'on-first-retry',   // record on failure
    screenshot:       'only-on-failure',
    video:            'retain-on-failure',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox',  use: { ...devices['Desktop Firefox'] } },
    { name: 'mobile',   use: { ...devices['iPhone 15'] } },
  ],
  webServer: {
    command:    'npm run dev',
    url:        'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

## Authentication Setup (Critical)

```typescript
// e2e/auth.setup.ts — runs ONCE before all tests
import { test as setup, expect } from '@playwright/test'
import path from 'path'

const authFile = path.join(__dirname, '../playwright/.auth/user.json')

setup('authenticate', async ({ page }) => {
  await page.goto('/login')
  await page.getByLabel('Email').fill(process.env.TEST_USER_EMAIL!)
  await page.getByLabel('Password').fill(process.env.TEST_USER_PASSWORD!)
  await page.getByRole('button', { name: 'Sign in' }).click()
  await expect(page).toHaveURL('/dashboard')

  // Save auth state — all subsequent tests reuse this
  await page.context().storageState({ path: authFile })
})
```

```typescript
// playwright.config.ts additions
export default defineConfig({
  projects: [
    {
      name:  'setup',
      testMatch: /auth\.setup\.ts/,
    },
    {
      name:         'authenticated',
      use: { storageState: 'playwright/.auth/user.json' },
      dependencies: ['setup'],
    },
    {
      name: 'unauthenticated',
      // no storageState — tests as logged-out user
    }
  ]
})
```

## Page Object Model

```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator, expect } from '@playwright/test'

export class LoginPage {
  readonly page:            Page
  readonly emailInput:      Locator
  readonly passwordInput:   Locator
  readonly submitButton:    Locator
  readonly errorMessage:    Locator

  constructor(page: Page) {
    this.page          = page
    this.emailInput    = page.getByLabel('Email')
    this.passwordInput = page.getByLabel('Password')
    this.submitButton  = page.getByRole('button', { name: 'Sign in' })
    this.errorMessage  = page.getByRole('alert')
  }

  async goto() {
    await this.page.goto('/login')
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email)
    await this.passwordInput.fill(password)
    await this.submitButton.click()
  }

  async expectError(message: string) {
    await expect(this.errorMessage).toContainText(message)
  }
}
```

## Writing Tests

```typescript
// e2e/tests/auth.test.ts
import { test, expect } from '@playwright/test'
import { LoginPage } from '../pages/LoginPage'

test.describe('Authentication', () => {
  test('shows error on invalid credentials', async ({ page }) => {
    const login = new LoginPage(page)
    await login.goto()
    await login.login('invalid@test.com', 'wrongpassword')
    await login.expectError('Invalid email or password')
  })

  test('redirects to dashboard after login', async ({ page }) => {
    const login = new LoginPage(page)
    await login.goto()
    await login.login(process.env.TEST_EMAIL!, process.env.TEST_PASSWORD!)
    await expect(page).toHaveURL('/dashboard')
    await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible()
  })
})
```

```typescript
// e2e/tests/checkout.test.ts (authenticated)
import { test, expect } from '@playwright/test'

test.describe('Checkout Flow', () => {
  test.use({ storageState: 'playwright/.auth/user.json' })

  test('user can complete a purchase', async ({ page }) => {
    // 1. Add to cart
    await page.goto('/products/widget-pro')
    await page.getByRole('button', { name: 'Add to cart' }).click()
    await expect(page.getByRole('status')).toContainText('Added to cart')

    // 2. Go to cart
    await page.getByRole('link', { name: 'Cart' }).click()
    await expect(page.getByRole('heading', { name: 'Your cart' })).toBeVisible()

    // 3. Checkout
    await page.getByRole('button', { name: 'Proceed to checkout' }).click()
    await expect(page).toHaveURL('/checkout')

    // 4. Fill shipping
    await page.getByLabel('Full name').fill('Jane Smith')
    await page.getByLabel('Address').fill('123 Main St')
    await page.getByRole('button', { name: 'Continue to payment' }).click()

    // 5. Confirm order (using test card)
    await page.getByLabel('Card number').fill('4242 4242 4242 4242')
    await page.getByLabel('Expiry').fill('12/28')
    await page.getByLabel('CVC').fill('123')
    await page.getByRole('button', { name: 'Place order' }).click()

    // 6. Verify confirmation
    await expect(page).toHaveURL(/\/orders\/\w+\/confirmation/)
    await expect(page.getByRole('heading', { name: /Order confirmed/i })).toBeVisible()
  })
})
```

## API Testing Within E2E

```typescript
// Use Playwright's request context to seed data before UI tests
test.beforeEach(async ({ request }) => {
  // Reset/seed via API — faster than UI
  await request.post('/api/test/seed', {
    data: { scenario: 'empty-cart' },
    headers: { 'x-test-secret': process.env.TEST_SECRET! }
  })
})
```

## Visual Regression Testing

```typescript
// Screenshot comparison
test('hero section matches design', async ({ page }) => {
  await page.goto('/')
  await expect(page).toHaveScreenshot('homepage-hero.png', {
    maxDiffPixelRatio: 0.02   // allow 2% pixel diff
  })
})

// Update golden screenshots: npx playwright test --update-snapshots
```

## CI Integration

```yaml
# .github/workflows/e2e.yml
name: E2E Tests
on: [push, pull_request]

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22 }
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npm run build && npm run start &
        env:
          TEST_USER_EMAIL:    ${{ secrets.TEST_USER_EMAIL }}
          TEST_USER_PASSWORD: ${{ secrets.TEST_USER_PASSWORD }}
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
```

## Test Organization

```
e2e/
├── auth.setup.ts          # auth state setup (runs once)
├── pages/                 # Page Object Model classes
│   ├── LoginPage.ts
│   ├── DashboardPage.ts
│   └── CheckoutPage.ts
├── tests/
│   ├── auth.test.ts       # authentication flows
│   ├── checkout.test.ts   # purchase flows
│   ├── search.test.ts     # search/filter flows
│   └── settings.test.ts   # user settings flows
├── fixtures/              # test data and helpers
│   └── users.ts
└── snapshots/             # visual regression baselines
```

## Selector Priority (Playwright)

1. `page.getByRole('button', { name: 'Submit' })` — most resilient
2. `page.getByLabel('Email')` — form inputs via label
3. `page.getByText('Welcome back')` — exact visible text
4. `page.getByPlaceholder('Search...')` — placeholder text
5. `page.getByTestId('submit-btn')` — data-testid attribute (last resort)
6. ❌ CSS selectors (`.btn-primary`) — avoid, breaks on refactoring
