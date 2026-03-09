---
title: "9.2 API Testing and E2E Testing"
description: "From rating endpoints to complete user flows—API testing in practice, Playwright E2E testing, flaky test troubleshooting, and reading test logs"
chapter: "Chapter 9"
---

# 9.2 API Testing and E2E Testing

> **Goal for this section**: Master the coverage strategy for API testing and practical methods for E2E testing, and learn to troubleshoot flaky tests and read test logs.

---

## Why Test APIs First

Xiao Ming decided to start writing tests. But where to begin? His app has frontend pages, backend APIs, and database operations—which should he test?

The senior dev said: "Start with the foundation—the API endpoints."

The reasons are practical. Xiao Ming's rating page was throwing errors. He opened the browser and saw "Rating submission failed." Where's the problem? It could be the frontend submit button not calling the API correctly, the API returning bad data, or the database write failing. Without API tests, he'd have to open the browser, log in, find the movie, click rate, and see the result—walking through the complete flow every time just to determine "is this a frontend or backend issue?"

But with API tests, he can send a request directly to `POST /api/movies/42/rate` and check the response. API returns 200 with correct data? Problem's in the frontend. API returns 500? Problem's in the backend. **API tests help you isolate issues to the "frontend or backend" level** without starting from the browser every time.

Another important reason: **APIs are far more stable than UIs**. Xiao Ming's rating page changes often—button moves, style changes, animations added. Every UI change risks breaking tests that depend on UI elements, even when functionality is intact. But API contracts stay relatively stable—"send this request, get this data" doesn't change because a button changed color. API tests written once last a long time; UI tests written once may need changes next week.

Think of it like checking a car: you can test if the engine starts (API testing), or take it for a drive to see overall performance (E2E testing). Engine tests are fast, stable, and easy to diagnose; road tests are comprehensive but time-consuming, and traffic or weather can affect results. Most problems surface during engine testing.

## What to Test for One Endpoint

Xiao Ming's rating endpoint is `POST /api/movies/:id/rate`, receiving a request body like `{ rating: 4 }`, writing the rating to the database, and returning the updated average. What scenarios should this endpoint cover?

**Happy path**: Logged-in user submits a valid rating (integer between 1-5), expects 200 and correct average. This is the basics—does the feature work at all?

**Parameter validation**: User submits rating 0 or 6 (outside 1-5 range), expects 400 with error message. User submits a string `"abc` instead of a number, expects 400. This tests "can the endpoint reject invalid input"—if the endpoint doesn't validate, dirty data ends up in the database.

**Authorization**: Unauthenticated user submits a rating, expects 401. This tests "does the endpoint check identity"—you added auth in Chapter 8, now verify it's actually working.

**Edge cases**: Same user rates the same movie twice—should it update the old rating or reject? Movie ID doesn't exist (like `/api/movies/99999/rate`), expects 404. These are business-specific scenarios. If you've already implemented duplicate rating logic in code, AI will see it when reading code and generate corresponding tests; but if this rule only exists in your head (you plan to implement but haven't written it yet), you need to proactively mention it.

**At minimum, cover these four categories per endpoint**: happy path, validation, authorization, edge cases. This isn't dogma, but a mindset—ask yourself when writing tests: does the normal case work? Are invalid inputs blocked? Can unauthorized users access it? Are there special cases I know but AI doesn't?

<APITestScenarios />

<details>
<summary>Curious? Expand to see: Complete rating endpoint tests</summary>

```typescript
import { describe, test, expect, beforeEach } from 'vitest'

describe('POST /api/movies/:id/rate', () => {
  // Happy path
  test('logged-in user submits valid rating', async () => {
    const res = await request(app)
      .post('/api/movies/42/rate')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ rating: 4 })

    expect(res.status).toBe(200)
    expect(res.body.averageRating).toBeCloseTo(4.0)
  })

  // Parameter validation
  test('rating out of range returns 400', async () => {
    const res = await request(app)
      .post('/api/movies/42/rate')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ rating: 6 })

    expect(res.status).toBe(400)
  })

  // Authorization
  test('unauthenticated user returns 401', async () => {
    const res = await request(app)
      .post('/api/movies/42/rate')
      .send({ rating: 4 })

    expect(res.status).toBe(401)
  })

  // Edge cases
  test('duplicate rating updates old record', async () => {
    // First rating
    await request(app)
      .post('/api/movies/42/rate')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ rating: 3 })

    // Second rating (same user, same movie)
    const res = await request(app)
      .post('/api/movies/42/rate')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ rating: 5 })

    expect(res.status).toBe(200)
    // Should update, not add new
    expect(res.body.averageRating).toBe(5)
  })

  test('non-existent movie returns 404', async () => {
    const res = await request(app)
      .post('/api/movies/99999/rate')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ rating: 4 })

    expect(res.status).toBe(404)
  })
})
```

</details>

## Let AI Generate API Tests for You

You're used to having Claude Code analyze your codebase and generate code—testing is no different. Reference your API folder and tell it to generate tests for each endpoint:

> Tell AI: "Read all routes in the `app/api` folder and generate Vitest tests for each API. Cover for each endpoint: normal request (200), parameter error (400), unauthorized (401)."

It will traverse your endpoint files, analyzing each route's parameters, return values, and middleware, generating corresponding test code. But you must review the results—not for syntax (AI rarely gets syntax wrong), but for coverage. The "four categories" mentioned above—AI usually covers scenarios corresponding to implemented logic after reading code. What you need to watch for: are there business rules you plan to implement but haven't written yet? Are there edge cases not explicitly handled in code (like concurrent submissions, extreme data volumes)? These are what you as the business domain expert need to supplement.

Another practical tip: how do you verify tests are actually "testing something" rather than going through motions? Try manually introducing a bug in business code (like changing rating validation from `1-5` to `1-10`), then run tests. If tests don't fail red, they're not really validating this constraint—you need to add an assertion for range boundaries. This "deliberately break code to see if tests catch it" method is called mutation testing, a great way to verify test quality.

## From Endpoints to UI: E2E Testing

Xiao Ming's API tests all passed. He breathed a sigh of relief, thinking the rating feature should be fine. Then a friend reported: "Clicked the rating button, nothing happened."

He investigated—the API itself was fine, direct requests returned normally. The problem was in the frontend: the rating button's click event wasn't calling the API at all. Xiao Ming had refactored components and moved submission logic to another file, but forgot to import it in the new component. TypeScript compiled without errors (function signatures were correct, just not called), and the page rendered fine (button was there, just did nothing when clicked). API tests couldn't catch this because they bypass the frontend and send requests directly.

This is the value of E2E testing: it tests the **complete user experience**—from opening the page, clicking buttons, to seeing results. It doesn't care "does the API work" or "is the function return value correct," it cares "can the user do what they want to do."

**Playwright** is currently the most mainstream E2E testing tool, open-sourced by Microsoft. It can automatically open browsers (Chrome, Firefox, Safari all supported), simulate user clicks, inputs, scrolling, and even take screenshots and record videos. Think of it as a "robot user"—it follows your script, operates your app like a real person, then checks if results match expectations.

Playwright has a particularly practical feature: **record mode**. You don't need to write test scripts from scratch—let Playwright record your operations: where you click, what you type, it automatically generates corresponding test code. The generated code may need tweaking (selectors might not be stable enough), but as a starting point it's much faster than writing from scratch. Run `npx playwright codegen http://localhost:3000` to start record mode.

Xiao Ming's first E2E test: user logs in and rates a movie.

<details>
<summary>Curious? Expand to see: Playwright test code</summary>

```typescript
import { test, expect } from '@playwright/test'

test('user rates a movie', async ({ page }) => {
  // Login
  await page.goto('/login')
  await page.fill('[name="email"]', 'ming@example.com')
  await page.fill('[name="password"]', 'password123')
  await page.click('button[type="submit"]')

  // Wait for login to complete, redirect to home
  await page.waitForURL('/')

  // Go to movie detail page
  await page.click('text=Interstellar')
  await page.waitForURL('/movies/**')

  // Click 4 stars
  await page.click('[data-testid="star-4"]')
  await page.click('[data-testid="submit-rating"]')

  // Verify average rating updated
  await expect(page.locator('.average-rating')).toContainText('4')
})
```

</details>

Note the `waitForURL` and `waitForURL('/movies/**')` in the code—these are **explicit waits**, telling Playwright "wait for page navigation to complete before continuing." Without waiting, the script might look for elements before the page finishes loading, fail to find them, and error. This is one of the most common E2E testing pitfalls, detailed below.

## Flaky Tests—the "Mysticism" of Testing

<FlakyTestAnalysis />

Xiao Ming wrote his first E2E test, ran it three times: pass, pass, fail. Same code, same environment, why different results?

This is a **flaky test**—sometimes passes, sometimes fails. Like a vending machine that occasionally eats money without dispensing—it's not your coins, the machine itself is unstable. Flaky tests are the most headache-inducing problem in E2E testing, encountered by almost everyone who writes E2E tests.

Why flaky? Usually these reasons:

**Async operation timing issues**. Xiao Ming clicks the "submit rating" button, test script immediately checks if the average rating updated on the page. But rating submission is async—the request went out, server hasn't responded, page hasn't updated, test already asserted. When local network is fast, request returns instantly, test passes; network slightly slower, request hasn't returned, test fails. Solution is **explicit waiting**: don't assert immediately after operation, wait for target element to appear or update before asserting. Playwright's `expect(locator).toContainText()` waits by default (5 seconds), but if your operation is especially slow (like file uploads or complex calculations), you may need to manually increase timeout.

**Data races**. Xiao Ming wrote two tests: Test A gives movie 42 a 5-star rating, Test B checks if movie 42's average is 0 (assuming no one rated it). If Test A runs first, Test B fails—because Test A's leftover data affected Test B. Solution is **data isolation**: each test independently prepares its own needed data, cleans up after test finishes. Don't let tests share state.

**Environment differences**. Local dev machine performs well, network is fast, tests run smoothly. But CI environment (GitHub Actions VMs) may perform worse, have higher network latency—same wait time works locally but not in CI. Solution is setting **reasonable timeout times** for assertions, don't assume operations complete instantly.

Flaky tests cannot be ignored. "Occasionally fails" means you can never trust test results—test passed, is it really fine, or just didn't trigger the flake this time? A hole in the safety net means you can't trust it. When encountering flaky tests, either fix them (find and resolve instability causes), or temporarily skip them (mark as `skip`) and document why, but don't let them keep "occasionally red" there.

How to tell if a test is flaky or really has a bug? Simple method: **run it 5 times consecutively**. If all 5 fail, probably a real bug; if some pass and some fail, it's flaky. Playwright supports `--repeat-each=5` parameter, running each test 5 times, making it easy to quickly determine.

Core principle for fixing flaky tests: **don't use `sleep` to "wait a bit"**. `await page.waitForTimeout(2000)` (wait 2 seconds) seems to solve the problem, but it just hides it—2 seconds works locally, may not in CI; works today, may not with larger data volumes later. Correct approach is **conditional waiting**: `await page.waitForSelector('.average-rating')` (wait for this element to appear), `await expect(locator).toContainText('4')` (wait for text to become expected value). Conditional waiting continues the moment conditions are met, neither waiting too long (wasting time) nor too short (causing flakiness).

> Tell AI: "This E2E test sometimes passes sometimes fails, help me analyze the cause and fix. Error message is: [paste error log]"

## Reading Logs When Tests Fail

Xiao Ming's E2E test failed, terminal output a bunch of info. He looked at the screen full of red text, didn't know where to start. Senior dev said: "Don't panic, three-step diagnosis."

**Step 1: Look at error type**. Test failure error messages usually fall into these types:

`Timeout waiting for selector "[data-testid='submit-rating']"` —this is a **wait timeout**, meaning Playwright couldn't find this element within the time limit. Possible causes: page hasn't finished loading, element's `data-testid` changed, previous operation (like login) failed causing page to stop at wrong place.

`Expected "4.0" but received "3.5"` —this is an **assertion failure**, element found but value wrong. Possible causes: calculation logic bug, test data not properly prepared (like other rating records already in database affecting average).

`Error: net::ERR_CONNECTION_REFUSED` —this is a **network error**, usually means backend service isn't running, or wrong port.

**Step 2: Look at screenshot**. Playwright automatically saves page screenshots on test failure, default in `test-results/` directory. Open the screenshot to see page state at that moment—stopped at login page (login failed)? Showing error message (API returned error)? Or completely blank page (frontend rendering crashed)? Screenshots are much more intuitive than logs, often one screenshot locates the problem.

**Step 3: Look at network requests**. If screenshot doesn't reveal problem, look at network requests during testing. Playwright can configure trace (tracking), recording every network request and response during testing. Open trace file, find that rating submission request—did it return 200 or 500? What data did it return? If API returned 500, problem's in backend; if API returned correct data but page didn't update, problem's in frontend rendering logic.

<details>
<summary>Curious? Expand to see: Enabling Playwright Trace</summary>

```typescript
// playwright.config.ts
export default defineConfig({
  use: {
    // Only save trace on first retry failure
    trace: 'on-first-retry',
    // Auto screenshot on failure
    screenshot: 'only-on-failure',
  },
})
```

View trace:

```bash
npx playwright show-trace test-results/xxx/trace.zip
```

This opens a visual interface where you can step through test playback, seeing page state, network requests, and console logs at each step.

</details>

**Error message → Screenshot → Network requests**, three steps down, most test failures can be located. If still can't find, throw error logs and screenshots to AI together, let it help analyze.

## When to Use E2E vs API Testing

Xiao Ming now has both API tests and E2E tests, but unsure when to use which. Senior dev gave a simple decision criteria:

**API tests cover "is the data correct"**—status codes, data formats, business logic returned by endpoints. Most feature core logic can be verified through API tests. Fast to write, fast to run, low maintenance cost.

**E2E tests cover "can the user use it"**—can pages open, can buttons be clicked, can flows be completed. Only use for most critical user flows: registration/login, core business operations (rating, search), payment flows. Don't write E2E tests for every page—maintenance costs will explode.

For Xiao Ming's "personal Douban," reasonable test allocation is: all API endpoints have integration tests (covering four categories), E2E tests only write two or three—"user registers and logs in," "searches movie and views details," "rates a movie." These three flows cover core user experience, leave rest to API tests.

A common misconception is "E2E tests are more realistic, so better." Realistic doesn't equal better—E2E tests are closest to user experience, but also have highest maintenance cost. Xiao Ming's rating page changed layout once, all E2E tests involving rating need selector updates; but API tests completely unaffected. If you find yourself spending more time maintaining E2E tests than writing new features, you've written too many E2E tests—push some logic down to API test layer.

Another decision criteria: **if a bug can only be found through E2E testing (like button not bound to event, wrong page navigation), use E2E test; if a bug can be found through API testing (like endpoint returning wrong data, auth not validated), use API test**. Use lighter weight verification when possible, don't use heavier methods.

---

::: tip Key Takeaways for This Section
- Test APIs first: fast, stable, easy to locate problems. At minimum four scenarios per endpoint: happy path, validation, authorization, edge cases
- E2E tests cover "user experience": only cover most critical flows, don't overuse
- Three main causes of flaky tests: async timing, data races, environment differences. Solve with explicit waits, data isolation, reasonable timeouts
- Three-step diagnosis for test failures: error message → screenshot → network requests
:::

::: info Next Step
Have tests but often forget to run them? Go to [Automation Workflow](./03-automation-workflow.md)—let Git Hooks and GitHub Actions help you remember "check every time."
:::