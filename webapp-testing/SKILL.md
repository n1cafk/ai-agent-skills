---
name: webapp-testing
description: Toolkit for interacting with and testing web applications using Playwright. Supports verifying frontend functionality, debugging UI behavior, and capturing screenshots.
license: Apache-2.0
source: skillcreatorai/Ai-Agent-Skills
---

# Web App Testing with Playwright

## Setup

```bash
npm init playwright@latest
```

## Basic Test Structure

```typescript
import { test, expect } from '@playwright/test';

test('homepage has title', async ({ page }) => {
  await page.goto('http://localhost:3000');
  await expect(page).toHaveTitle(/My App/);
});
```

## Common Actions

### Navigation
```typescript
await page.goto('http://localhost:3000');
await page.goBack();
await page.reload();
```

### Clicking
```typescript
await page.click('button');
await page.click('text=Submit');
await page.click('#submit-btn');
await page.click('[data-testid="submit"]');
```

### Form Input
```typescript
await page.fill('input[name="email"]', 'test@example.com');
await page.fill('#password', 'secret123');
await page.selectOption('select#country', 'USA');
await page.check('input[type="checkbox"]');
```

### Waiting
```typescript
await page.waitForSelector('.loaded');
await page.waitForURL('**/dashboard');
await page.waitForResponse('**/api/data');
```

## Assertions

```typescript
await expect(page.locator('h1')).toHaveText('Welcome');
await expect(page.locator('.items')).toHaveCount(5);
await expect(page.locator('button')).toBeEnabled();
await expect(page.locator('.modal')).toBeVisible();
```

## Screenshots

```typescript
await page.screenshot({ path: 'screenshot.png', fullPage: true });
await page.locator('.chart').screenshot({ path: 'chart.png' });
```

## Network Interception

```typescript
await page.route('**/api/data', route => {
  route.fulfill({
    status: 200,
    body: JSON.stringify({ items: [] })
  });
});
```

## Running Tests

```bash
npx playwright test              # Run all
npx playwright test --headed     # With browser UI
npx playwright test --ui         # Interactive UI
```
