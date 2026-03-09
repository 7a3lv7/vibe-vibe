---
title: "9.1 Why We Need Tests"
description: "Regression testing, the testing pyramid, when to automate—understanding the core value of testing through Xiaoming's rating feature"
chapter: "Chapter 9"
---

# 9.1 Why We Need Tests

> **Goal of this section**: Understand the concept of regression testing, grasp the layered logic of the testing pyramid, know when to introduce automated testing, and how to let AI help you write tests.

---

## What Exactly Is a "Safety Net"

By Chapter 9, you've probably already seen test files in your project—Claude Code sometimes creates `*.test.ts` when generating code, or you've seen `__tests__` directories in other people's projects. But "having seen tests" and "understanding the value of tests" are two different things. Xiaoming was like this: he knew the concept of testing, but always felt "I can just test manually, why write test code." Until he fell into the "whack-a-mole" dilemma from the preface—fixing ratings broke search, fixing search broke the detail page—only then did he start thinking seriously about this problem.

The veteran developer said he needed a "safety net." Xiaoming asked: "What exactly does that mean?"

The veteran developer gave an example using Xiaoming's app. Xiaoming's "personal Douban" now has these features: movie list, search, detail page, user registration and login, movie ratings. Each feature has code logic behind it—search queries the database, ratings write to the database, detail pages stitch together multiple data sources. These features aren't completely independent; they share database connections, common utility functions, and route configurations. Change one place, and another might be affected.

The veteran developer drew a simple diagram: the rating feature calls the common function `db.query()` to write rating records, and the search feature also calls `db.query()` to query movies. When Xiaoming added "sort by time" to the rating feature, he modified `db.query()`'s default sort parameter—he thought this only affected ratings, but the search feature also used this function, and the search results' sorting was unexpectedly changed. This "fix A, break B" situation becomes more frequent as the codebase grows larger, because dependencies between modules become increasingly complex, and it's hard to track all impact chains in your head.

A "safety net" is a set of **automated checks**. Each check corresponds to a feature point: does searching for "sci-fi" return results? Can you successfully rate a movie 4 stars? Will unauthenticated users be rejected when accessing the rating API? These checks are written as code and stored in the project. Every time you finish changing code, run these checks—all passing means your changes didn't accidentally break other features; any failure means something was broken by your recent changes, and you should investigate immediately.

This is **regression testing**. The word "regression" sounds academic, but the meaning is simple: **did features that worked before still work after changing other code?** Xiaoming breaking search by modifying rating logic is a classic regression bug. If he had an automated check for the search feature, running it after changing rating code would immediately show search failing red—he could discover the problem on the spot, without waiting for friends to report bugs.

Manual regression can also be done—after each code change, open the browser and manually click through all features. Honestly, clicking through isn't slow; a dozen features can be covered in a few minutes, and manual testing has unique value: you're simulating real user paths and can discover experience issues that automated tests miss—"this button's click area is too small," "the page flashes after loading," "this prompt text reads strangely." These are things machines can't perceive.

What really becomes unbearable isn't "clicking through is slow," but **repetition**. Xiaoming changed rating logic today, clicked through, no problem. In the afternoon he changed search sorting, clicked through again. At night he fixed a style bug, had to click through again. Three code changes in a day means three rounds of clicking, and each time he has to remember "which scenarios did I test last time, can't miss any this time." The first round you'll click carefully, the second round you start going through the motions, the third round you might skip parts that "seem like they won't have problems"—and bugs often hide in the parts you skipped. Automated testing doesn't solve the problem of "manual testing is too slow," but rather **the fact that "completely and without omission repeating every time" is something humans can't do but machines can**.

And humans make judgments—"I only changed ratings, search should be fine"—then skip testing search. This judgment is mostly right, but occasionally wrong, and that occasional wrong time is when bugs happen. Machines don't make these "smart" judgments; you tell it to check 15 points, and it honestly checks all 15, every time, consistently.

Another easily overlooked benefit: **automated tests are living documentation**. Three months later when Xiaoming looks back at his code, he can't remember what format the rating API should return. But the test file makes it crystal clear—`expect(res.status).toBe(200)`, `expect(res.body.averageRating).toBeCloseTo(4.0)`. Test cases are more reliable than comments because comments can become outdated (code changed but comment wasn't), but if tests become outdated they fail red.

## The Testing Pyramid—Not All Tests Are Equal

Xiaoming understood the value of regression testing, but a new question arose: "How should I write these checks? Open the browser to simulate user clicks, or directly call functions to see return values?"

The veteran developer said: "Both exist, but their costs and values differ."

Let's look at Xiaoming's rating feature specifically. His app has a function `calculateAverageRating` that receives an array of ratings and returns the average. For example, input `[4, 5, 3]`, expected output `4`. Testing this function is very simple: call it directly and check if the return value is correct. No need to start a server, no database connection, no browser—pure input-output validation. Runs in milliseconds. This is **unit testing**: testing the smallest code unit (a function, a utility method), extremely fast, completely isolated.

The value of unit testing lies in **precise localization**. If the `calculateAverageRating` test fails, you immediately know the problem is in this function—no guessing whether it's a database issue, network issue, or browser issue. And because it doesn't depend on any external environment, it's extremely stable—won't "occasionally fail" because the database is down or the network is slow. Other parts of Xiaoming's app suitable for unit testing include: search keyword sanitization functions (removing special characters), date formatting functions, rating star display logic (how many stars to show for 4.7 points). These are all pure input-output logic, independent of external environments.

Going up a level. Xiaoming's rating feature has an API endpoint `POST /api/movies/42/rate` that receives user-submitted ratings, writes to the database, and returns the updated average. Testing this endpoint requires starting a server, connecting to a database (or test database), sending an HTTP request, checking the returned status code and data, then querying the database to confirm the rating record was actually written. Slower than unit testing (needs network and database), but it tests the result of "multiple modules working together"—route parsing, request validation, database operations, response formatting—can these links work together properly? This is **integration testing** (also called API testing): testing collaboration between modules, medium speed, requires real dependencies (database, server).

Integration testing can find issues that unit testing misses. For example, the `calculateAverageRating` function itself is fine, but when the API route passes the user-submitted rating to this function, it forgot to convert the string to a number—`"4"` and `4` behave differently in JavaScript. Unit testing only tests the function itself, missing this "seam" issue; integration testing starts from the HTTP request, goes through the complete processing chain, and can expose this kind of module coordination problem.

Going up further. Xiaoming wants to test the complete "user rates a movie" experience: open browser, log in, find a movie, click stars, submit rating, check if the average on the page updated. This requires starting a real browser, simulating mouse clicks and keyboard input, waiting for page loading and rendering. One run might take several seconds or even over ten seconds. And it's very "fragile"—button moved, loading too slow, popup blocking elements, all can cause test failure even when the feature itself works fine. This is **E2E testing** (end-to-end testing): simulating complete real user operation flows, closest to real experience, but slowest, most fragile, highest maintenance cost.

These three layers form the classic **testing pyramid**. Imagine a physical exam: blood test is the most basic check, fast, cheap, can find most common issues (unit testing); ultrasound goes deeper, checking organ coordination (integration testing); full-body CT is most comprehensive, but expensive, time-consuming, not done every checkup (E2E testing). You don't get a CT scan for every cold; similarly, you don't write E2E tests for every feature.

The pyramid shape shows quantity distribution: most unit tests at the bottom (fast, stable, cheap, write more without worry), moderate integration tests in the middle (covering core APIs), fewest E2E tests at the top (only testing most critical user flows). For Xiaoming's rating feature: pure functions like `calculateAverageRating` get unit test coverage, the `POST /api/movies/:id/rate` endpoint gets integration test coverage, and the complete flow "user logs in → finds movie → rates → sees average" gets one E2E test. Most logic can be verified at the bottom and middle layers without starting a browser for every scenario.

Why not reverse it—all E2E tests, one step to completion? Because the cost gap is huge. A unit test runs in milliseconds, an E2E test runs in seconds to over ten seconds. If Xiaoming's app has 50 test scenarios, all unit tests finish in under 1 second; all E2E tests might take 5 minutes. And E2E tests have high maintenance costs—UI changed a button position, all E2E tests involving this button need updating; but unit and integration tests are completely unaffected because they don't depend on UI. The pyramid isn't dogma, it's economics: get the highest confidence at the lowest cost.

Xiaoming might ask: "Then how do I know which layer to use for a feature?" A simple criterion: **if this logic can be verified without depending on UI, don't use E2E testing**. Is rating calculation correct? Unit test. Is the data returned by the API correct? Integration test. Can users complete the operation by clicking buttons? Only then do you need E2E. The further down you push, the faster, more stable, and cheaper the test.

<TestPyramid />

<details>
<summary>Curious? Expand to see: what code looks like for all three layers</summary>

```typescript
// Unit test: testing pure functions, no external dependencies needed
test('calculateAverageRating calculates average', () => {
  expect(calculateAverageRating([4, 5, 3])).toBe(4)
  expect(calculateAverageRating([5])).toBe(5)
  expect(calculateAverageRating([])).toBe(0) // boundary: empty array
})

// Integration test: testing API endpoint, needs server and database
test('POST /api/movies/:id/rate submits rating', async () => {
  const response = await request(app)
    .post('/api/movies/42/rate')
    .set('Authorization', `Bearer ${token}`)
    .send({ rating: 4 })

  expect(response.status).toBe(200)
  expect(response.body.averageRating).toBe(4)
})

// E2E test: simulating user operations, needs browser
test('user rates a movie', async ({ page }) => {
  await page.goto('/movies/42')
  await page.click('[data-testid="star-4"]')
  await page.click('[data-testid="submit-rating"]')
  await expect(page.locator('.average-rating')).toHaveText('4.0')
})
```

</details>

## When to Introduce Automated Testing

Xiaoming was convinced, but then asked: "Do I need to write tests for all features right now?"

The veteran developer said: "Not necessarily. Automated testing is an investment—writing tests takes time, maintaining tests also takes time. The investment needs to pay off."

When is the payoff obvious? Look at Xiaoming's situation: his app has 5+ pages, core business flow (search → detail → rate) has 3+ steps, and he's already encountered the "fix A, break B" problem. These signals indicate his project complexity has exceeded what manual regression can reliably cover—time to automate.

<TestCoverageCalculator />

Another less obvious but equally important signal: **you start being afraid to change code**. Xiaoming knows the search feature's code isn't well written and wants to refactor it, but he doesn't dare—what if it breaks? Last time changing ratings broke search, will changing search break something else this time? This "afraid to touch" mindset is the beginning of code rot—you know the code has problems, but don't dare fix them for fear of introducing new bugs, so the code gets worse and worse. Automated testing breaks this vicious cycle: with a safety net, you can refactor with confidence, run tests after changes, green means no problem.

When shouldn't you? If you're still in the rapid prototyping phase where requirements change constantly, today's rating feature might be completely scrapped for likes tomorrow, then writing tests is wasted effort—tests can't keep up with requirement changes, writing them is pointless. One-off projects (event pages that won't be maintained after completion), purely static display pages (with no interactive logic to test), also don't need automated testing.

Another common pitfall: **writing tests for coverage numbers**. Some tools tell you "your code coverage is only 30%," and you might feel you should raise it to 80% or even 100%. But coverage is a misleading metric—it only tells you "which code was executed," not "whether tests verified correct behavior." You can write a test that calls all functions but makes no assertions, 100% coverage, but tested nothing. Conversely, a well-designed test might only cover 20% of code, but exactly cover the most error-prone 20%. Focus on the **value** of tests (can they help you find real bugs), not the **number** of coverage.

Another easily overlooked point: **what to test first**. Xiaoming doesn't need to write tests for all features at once. Priority is clear—test core business logic first (rating calculation, search sorting—functions where "if calculated wrong, users will notice"), then API endpoints (the gateway for data in and out), and only then consider E2E (only covering most critical user flows). Use 20% of tests to cover 80% of risk, this is more practical than pursuing 100% coverage.

A practical priority judgment method: ask yourself "how serious are the consequences if this feature breaks?" Rating calculation wrong, users see incorrect averages—serious, test first. Movie list sorting changed from "by rating" to "by time"—not very serious, can test later. 404 page style slightly off—doesn't matter, don't test. Spend limited testing effort on places where "breaking would really hurt."

## The Right Way to Use AI-Assisted Testing

By Chapter 9, you've already had Claude Code write large amounts of business code for you—from database Schema to API routes to frontend pages. Test code is the same, it's completely capable. You tell it "write tests for the rating endpoint, covering normal, error, and boundary scenarios," and it will analyze your endpoint code and generate complete test files, including test data preparation and cleanup.

But there's a key difference between tests and business code: business requirements are usually in PRDs or your descriptions, which AI can directly understand; but the value of tests depends on "whether you're testing critical scenarios," which requires your understanding of the business. Claude Code reads your codebase, so it can see and test all implemented logic—if your code has duplicate rating handling logic, it will generate corresponding tests. But if a business rule isn't implemented yet, only exists in your head (like "when the same user rates again, should update old rating instead of adding new," and you haven't written this logic), AI won't think to test it. These "business rules not yet written into code" are what you need to supplement.

So the correct division of labor is: **you control testing strategy, AI executes and supplements**. You can have AI first analyze the codebase and suggest testing priorities—its understanding of code structure is often more comprehensive than yours. But the final judgment of "is this scenario worth testing" and "is this boundary case important" is in your hands, because you understand business context and user behavior better than AI. You review the generated tests—not for syntax correctness (AI rarely writes wrong syntax), but for "are they testing the right things." Missing critical scenarios? Add them. Testing a bunch of impossible situations? Delete them, reduce maintenance burden.

Specifically, when reviewing AI-generated tests, focus on two things: **coverage**—are all four scenario types (normal, validation, permission, boundary) covered? Especially boundary scenarios, if the corresponding business rule isn't implemented in code yet, AI won't think of them out of thin air. **Realism**—are the test data and scenarios close to real usage? Test data that's too "clean" (all English, all positive integers) might miss real environment issues (Chinese input, special characters, floating point precision).

> Tell AI: "Read my project code and analyze which modules most need testing. List suggested testing priorities: which should get unit tests first, which need integration tests, which need E2E tests."

The value of this prompt: it lets AI do the first round of analysis for you, but final priority judgment remains in your hands. AI might suggest "all APIs need integration tests," but you know some endpoints are internal and rarely change, so priority can be lower.

---

::: tip Key Takeaways from This Section
- The core question of regression testing: did features that worked before still work after changing other code?
- Testing pyramid: many fast unit tests (testing functions), moderate integration tests (testing APIs), few precise E2E tests (testing flows)
- Automated testing is an investment: 5+ pages, core flow 3+ steps, encountered "fix A, break B"—these signals indicate it's time
- AI helps you write test code, you judge "are the tests correct"—business-specific boundary cases need your supplementation
:::

::: info Next Step
Having understood why we need tests and what to test, proceed to [API Testing and E2E Testing](./02-api-and-e2e-testing.md)—starting from Xiaoming's rating endpoint, hands-on with API testing and E2E testing.
:::