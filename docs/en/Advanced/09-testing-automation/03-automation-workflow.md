---
title: "9.3 Automated Workflows"
description: "Git Hooks, GitHub Actions CI, and TDD basics—let the machine remember what needs checking every time"
chapter: "Chapter 9"
---

# 9.3 Automated Workflows

> **Goal for this section**: Configure Git Hooks and GitHub Actions to run tests automatically; understand the mindset behind TDD and when it applies.

---

## You Have Tests, But Forget to Run Them

Xiaoming spent an afternoon writing tests for his core API endpoints. Everything passed on the first run—he felt accomplished. Then he moved on to new features: adding movie reviews, tweaking search rankings, optimizing detail page loading. After each change, he thought "I'll run the tests later," but always got distracted by the next feature and forgot.

Three days later, he pushed code to GitHub. His friend pulled it down and ran the tests—two failed. Xiaoming traced back and found a bug introduced when he changed the search ranking on day one. If he had run tests that day, he would've caught it immediately. But three days later, with dozens of changes in between, finding which commit caused the problem cost him several times more effort.

This story reveals a pattern: **the later you find a bug, the more expensive it is to fix**. Catch it right after coding, and you still remember what you changed—fixed in minutes. Wait until tomorrow, and you have to recall yesterday's work. Wait a week, and you've completely lost context; you need to re-understand the code just to fix it. The core value of automated testing isn't "finding bugs"—you can find bugs manually too—but **finding bugs early**, when they're cheapest to fix.

The old-timer says: "People forget. Machines don't. Let the machine remember."

## Git Hooks—Automatic Checks Before Commit

Git has a built-in mechanism called **Hooks**—scripts you configure to run automatically at specific Git operations. For example, the `pre-commit` hook runs before every `git commit`. If the script fails (returns a non-zero exit code), Git blocks the commit.

<GitHooksWorkflow />

Think of it like an access control system: you swipe your card, the system checks permissions automatically—you don't need to "remember" to check. Every time Xiaoming runs `git commit`, Git automatically runs `pnpm test`. All tests pass? Commit succeeds. Any test fails? Commit blocked—fix it first.

The psychological effect matters more than the technical one. Xiaoming used to think "I'll run tests later," but "later" never came. With a pre-commit hook, he doesn't need to "remember"—the act of committing triggers the tests. Like not needing to "remember" to lock the door because the system locks automatically. Turning "should do but easy to forget" into "happens automatically" is the core value of automation.

The most popular tool for managing Git Hooks is **Husky**. It handles hook scripts for you—no manual fiddling in `.git/hooks/`. Tell Claude Code what you need, and it'll set it up:

> Tell AI: "Help me configure a Git pre-commit hook that automatically runs `pnpm test` before each commit. Block the commit if tests fail. Use Husky."

<details>
<summary>Curious? Here's what Husky configuration looks like</summary>

```bash
# Install Husky
pnpm add -D husky

# Initialize
npx husky init

# .husky/pre-commit file content
pnpm test
```

That's it. From now on, every `git commit` triggers `pnpm test` via Husky. Tests fail? Commit blocked. Tests pass? Normal commit.

If you only want to run tests related to current changes (not all tests), you can use `lint-staged`—but for Xiaoming's project size, running all tests takes just seconds, so optimization isn't needed.

</details>

But pre-commit hooks have a limitation: **they only run locally**. Someone can bypass the hook with `git commit --no-verify` (rushing to commit first, fix later), and bad code still gets through. Plus, local environments differ—tests pass on your machine but might fail on another (depending on local environment variables, stale build artifacts, etc.). So Git Hooks are the first line of defense, not the only one.

Xiaoming might ask: "When should I use `--no-verify`?" There are legitimate scenarios: WIP commits (code unfinished, just saving progress), documentation-only changes (tests irrelevant), or when the hook checks something clearly unrelated to your changes. The key is **conscious skipping**, not making it a habit—if you find yourself constantly bypassing hooks, they're probably too heavy (running full test suites when only related tests are needed). Optimize the hook instead of working around it.

## GitHub Actions—Automatic Validation After Push

Once code reaches GitHub, **GitHub Actions** runs your tests on a fresh, clean virtual machine. This is the second line of defense—even if someone skipped the local hook, CI catches it.

Like a factory quality inspection line: every product gets checked before shipping. Workers might skip self-checks (local hooks), but the line's detectors catch everything (CI).

GitHub Actions' value isn't just "running tests again." It starts from zero in a **clean environment**—install dependencies, build, run tests. This catches "works on my machine" problems: you have an environment variable but forgot to add it to `.env.example`; cached build artifacts make tests accidentally pass locally; globally installed tools not declared in `package.json`. These issues hide locally but get exposed in CI's clean environment.

> Tell AI: "Write me a GitHub Actions workflow that automatically runs tests on every push and PR."

<details>
<summary>Curious? Here's what the GitHub Actions workflow file looks like</summary>

```yaml
# .github/workflows/test.yml
name: Test

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Setup pnpm
        uses: pnpm/action-setup@v4

      - name: Install dependencies
        run: pnpm install

      - name: Run tests
        run: pnpm test
```

Every push or PR triggers this flow on a fresh VM. Results show on the PR page—green check for pass, red X for failure.

</details>

Two defenses working together: Git Hooks catch obvious issues locally (fast feedback, seconds), GitHub Actions do final verification remotely (comprehensive check, maybe minutes). Most problems get caught locally; only rare "environment-specific" issues need CI.

Xiaoming felt a sense of relief seeing that green check on his PR—not "I think the code is fine," but "a clean environment verified from scratch that the code is fine." This certainty is something local testing can't provide, since local environments always have "only on my machine" special conditions.

One practical tip: if CI tests fail but local passes, don't keep rerunning locally. Check CI logs first—they tell you exactly which test failed and why. Common causes: missing environment variables (no `.env` file on CI, configure in GitHub repo Settings → Secrets), database connection failures (no local DB instance on CI), or timezone differences (CI defaults to UTC, you might be UTC+8). These are "environment issues," not "code issues"—fix by aligning CI with local environment.

<CIWorkflow />

## TDD—Think First, Code Second

Xiaoming wants to add a new feature to "Personal Douban": movie reviews. Users can write text comments up to 500 characters. The old-timer suggests: "Try writing tests first this time."

Xiaoming is confused: "How do I test code that doesn't exist?"

The old-timer says: "First think clearly about what this endpoint receives and returns—that's your test."

Xiaoming thinks: `POST /api/movies/42/reviews` receives `{ content: "Amazing visuals, stunning effects" }`, returns `201` and review object. No login? `401`. Empty content? `400`. Over 500 chars? `400`.

He writes these as test code. Tests fail, of course—the endpoint doesn't exist yet. Then he tells Claude Code: "Here's my test file, implement the corresponding API to make all tests pass."

After finishing, he notices something interesting: because he clarified the interface design first (parameters, status codes, rejections), coding was smooth. No mid-coding interface changes, no "this parameter should be named differently" halfway through. The test file itself is an **interface specification**—it precisely describes behavior, more accurate than docs or verbal descriptions because it's executable. If behavior diverges from the test, it immediately goes red.

This is the core of **TDD (Test-Driven Development)**: write tests first (define "what's correct"), then code (make tests pass), finally refactor (optimize code while keeping tests green). Like drawing blueprints before building a house—you don't start walls then wonder where the door goes.

TDD's three steps have names: **Red** (write test, it fails because code doesn't exist) → **Green** (write minimal code to pass) → **Refactor** (optimize structure, keep green). "Minimal code" matters—it prevents over-engineering. You don't need to consider "what if we support half-star ratings later?" Just pass current tests. When requirements change, update tests first (define new "correct"), then code.

But TDD isn't dogma. It fits best for: **core business logic** (rating calculations, permission checks—mistakes are costly), **stable interfaces** (API endpoints—hard to change once designed), **complex condition branches** (many inputs map to many outputs—list all cases before coding, less likely to miss).

Scenarios unsuitable for TDD are clear: **UI styling tweaks** (button colors, spacing—testing is pointless), **exploratory prototypes** (requirements unset, today's tests obsolete tomorrow), **one-off scripts** (data migration scripts run once and discarded—not worth testing).

Xiaoming doesn't need TDD for everything. But for core interfaces, the habit of "tests first, code second" is worth cultivating—it forces you to think through "how should this work" before typing.

TDD pairs exceptionally well with AI coding. Traditional TDD's pain point was "writing tests takes time," but now you describe interface behavior in natural language and let AI generate test code. Your job shifts from "writing test code" to "describing how the interface should work"—which is design. Then hand the test file to AI for implementation. Tests become a "contract" between you and AI—you define behavior, AI implements it, tests verify it. This workflow beats "code first, test later" because acceptance criteria are defined upfront; AI doesn't need to guess what you want.

<details>
<summary>Curious? Here's the complete TDD flow</summary>

```typescript
// Step 1: Write tests first (RED—failing because endpoint doesn't exist)
describe('POST /api/movies/:id/reviews', () => {
  test('logged-in user submits review', async () => {
    const res = await request(app)
      .post('/api/movies/42/reviews')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ content: 'Amazing visuals, stunning effects' })

    expect(res.status).toBe(201)
    expect(res.body.content).toBe('Amazing visuals, stunning effects')
    expect(res.body.movieId).toBe(42)
  })

  test('empty content returns 400', async () => {
    const res = await request(app)
      .post('/api/movies/42/reviews')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ content: '' })

    expect(res.status).toBe(400)
  })

  test('content over 500 chars returns 400', async () => {
    const res = await request(app)
      .post('/api/movies/42/reviews')
      .set('Authorization', `Bearer ${userToken}`)
      .send({ content: 'a'.repeat(501) })

    expect(res.status).toBe(400)
  })

  test('no login returns 401', async () => {
    const res = await request(app)
      .post('/api/movies/42/reviews')
      .send({ content: 'Amazing' })

    expect(res.status).toBe(401)
  })
})

// Step 2: Write code to pass tests (GREEN)
// Tell Claude Code: "Here's my test file, implement the corresponding API."

// Step 3: Refactor (REFACTOR)
// With all tests green, optimize code structure. Run tests after each change to confirm nothing broke.
```

</details>

## From "Whack-a-Mole" to "Safety Net"

Back to Xiaoming's story. A month ago, he coded in fear—every commit worried "what did I break this time?" Bug reports from friends were his only feedback, always late.

Now it's different. Core interfaces have API tests; key flows have E2E tests. Every `git commit`, Husky runs tests automatically—all green, commit with confidence. Code pushes to GitHub, Actions runs again—all green, merge with confidence. A test goes red? He knows immediately which change caused it, no waiting for bug reports.

Tests aren't a burden—they're **confidence to change code**. Without tests, you fear refactoring—what if it breaks? With tests, you refactor boldly—run them after, green means good. Fuller coverage means more confidence. This shift is gradual: the first time tests catch a bug for you ("thank goodness for tests, this would've shipped"), you understand their value isn't theoretical—it's real.

This wasn't built overnight. Xiaoming's tests accumulated gradually: start with the riskiest interfaces, add E2E for core flows, then layer on Git Hooks and CI. Every bug encountered gets a test after fixing—ensuring it never returns. This strategy is "bug-driven testing": not predicting all possible bugs, but patching defenses after each bite. The test suite grows slowly, the safety net tightens.

Don't chase perfect coverage. 80% of risk is covered by 20% of tests. Protect the critical parts first, fill in the rest over time. What matters is **starting**—even one test beats zero. Starting today: next time you fix a bug, write a test to ensure it stays fixed. That's your first step.

---

::: tip Key Takeaways
- Git Hooks (Husky): Automatically run tests before commit, block if tests fail—first line of defense
- GitHub Actions: Run tests in clean environment after push, catch "works on my machine" issues—second line of defense
- TDD fits core business logic and stable interfaces: define behavior with tests first, implement code after—not dogma, but a tool
- Tests accumulate gradually: protect critical parts first, add tests when fixing bugs, safety net tightens over time
:::

::: info Next Steps
Chapter 9 ends here. You now have testing fundamentals and automated workflows. Head to [Chapter 10: Localhost and Public Access](../10-localhost-public-access/index.md)—move your application from local to the internet.
:::