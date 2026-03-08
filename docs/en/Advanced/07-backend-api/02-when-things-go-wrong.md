---
title: "7.2 When APIs Go Wrong"
description: "Parameter validation, idempotency, error handling, connection pools—the first batch of issues after going live"
chapter: "Chapter 7"
---

# 7.2 When APIs Go Wrong

> **Section Goal**: Understand the most common types of issues after API deployment—dirty data, duplicate submissions, error handling, resource exhaustion—and how to describe these problems to AI so it can help you fix them.

---

## Xiaoming's App Goes Live

Xiaoming's "personal Douban" is finally complete. The movie list has pagination, filtering, and sorting. The detail page returns all data in one API call. The experience is smooth. He shares the link in his friend group for everyone to try.

For the first three days, everything is calm.

On the fourth day, problems begin.

---

## An Empty Title Movie Appears in the Database

Xiaoming opens Drizzle Studio to check the data and finds a strange record: the title is empty, the year is 0, and the director ID points to a non-existent director. He's confused—the frontend clearly has input validation. The submit button is disabled when the title is empty, and the year input only accepts numbers. How could such data be submitted?

He asks his friend, who says: "I tested your API directly with Postman, didn't pass any parameters, and it still succeeded."

That's when Xiaoming realizes: **frontend validation only stops "normal" users**. Anyone can bypass the frontend—open browser dev tools and send requests directly, use Postman or curl to call the API, or even write a script for batch calls. Frontend input restrictions, button disabling, and format checks are useless against these methods.

It's like a mall with a security guard at the entrance who reminds you to "please use the main entrance." But if someone comes in through a side door, back door, or even climbs through a window, the guard can't stop them. The real security check is inside—every counter verifies your identity and purchase eligibility.

**Frontend validation is for user experience** (instant feedback, telling users "title cannot be empty" without waiting for server response), **backend validation is for data security** (guarding the bottom line—no matter where the request comes from, dirty data cannot enter the database). Both are indispensable.

### Zod—The Validation Library AI Uses

When you tell AI "add parameter validation to the API," it will most likely use **Zod**. Zod is the most popular validation library in the TypeScript ecosystem. You don't need to learn its syntax, just know three things:

- **What it does**: Define rules for "what data should look like" (e.g., title must be a 1-100 character string, year must be an integer between 1888-2030), then automatically validate incoming data
- **What happens when validation fails**: Return specific error messages to the frontend, telling it which field is wrong and why
- **Why use it instead of hand-written if-else**: Zod combines validation rules and TypeScript types—define validation rules, and TypeScript types are automatically inferred. No need to write twice. Plus Zod's error message format is consistent, making frontend handling convenient

<details>
<summary>Curious what Zod looks like? Expand to see, or skip if you prefer</summary>

```typescript
import { z } from 'zod'

// Define rules: title must be 1-100 character string, year is 1888-2030 integer
const createMovieSchema = z.object({
  title: z.string().min(1, 'Title cannot be empty').max(100, 'Title too long'),
  year: z.number().int('Year must be integer').min(1888, 'Year cannot be earlier than 1888').max(2030),
  directorId: z.number().int().positive('Director ID must be positive integer'),
})

// Use in API
export async function POST(request: Request) {
  const body = await request.json()
  const result = createMovieSchema.safeParse(body)

  if (!result.success) {
    // Validation failed, return 400 with specific error message
    return Response.json(
      { success: false, error: { message: result.error.issues[0].message } },
      { status: 400 }
    )
  }

  // result.data is validated safe data, type automatically inferred
  const newMovie = await db.insert(movies).values(result.data).returning()
  return Response.json({ success: true, data: newMovie[0] })
}
```

Note the `safeParse` method—it doesn't throw exceptions on validation failure, but returns an object containing `success` and `error`, letting you decide how to handle it. This is more controllable than `try-catch`.

</details>

### How Strict Should Validation Be?

Xiaoming asks the veteran: "Do I need to add a bunch of validation rules to every field?"

The veteran says: "Depends on the scenario. The core principle is—**don't trust any external input, but don't over-validate either**."

| Must Validate | Reason |
|---------------|--------|
| Required fields cannot be empty | Empty data in database causes various downstream issues |
| String length limits | Prevent someone from passing a 10MB string that blows up memory |
| Number range limits | Rating cannot be -1 or 999, year cannot be 3000 |
| Enum value validation | Status can only be "draft"/"published"/"archived", not arbitrary strings |
| Foreign key existence | directorId must point to an actual director (database foreign key constraints will also block, but early validation gives friendlier error messages) |

| No Need to Over-Validate | Reason |
|--------------------------|--------|
| Internal service calls | Your own frontend calling your own backend, data format is under your control, basic validation is enough |
| Fields with database constraints | If database already has UNIQUE constraint, no need to check "is there a duplicate" in code |

Tell AI:

> "Add Zod parameter validation to all POST and PATCH endpoints. Required fields cannot be empty, strings have reasonable length limits, numbers have reasonable range limits. Return 400 with specific error messages on validation failure, using format `{ success: false, error: { message: '...' } }`."

---

<ErrorHandling />

## The Same Movie Added 10 Times

Xiaoming's friend messages: "I clicked the add button, the network was a bit slow, so I clicked a few more times. Now 'Spirited Away' appears 10 times..."

Xiaoming opens the database and sure enough—the same movie has 10 identical records, only the IDs differ.

This is a classic problem: **duplicate submission**. The user didn't do it on purpose, but network latency made them think the first click didn't work, so they clicked a few more times. Each click sent a POST request, and the backend dutifully created 10 records.

### Frontend Blocks One Layer, Backend Catches Another

**Frontend level**—immediately disable the button after clicking, show "Submitting...", and restore when the request returns. This blocks 90% of duplicate submissions. But it can't block all cases—like users refreshing the page to resubmit, automatic retries after network timeout, or someone calling the API with a script.

**Backend level**—make the API **idempotent**.

Idempotency is a mathematical concept, but it's easy to understand with everyday examples:

- **Elevator buttons** are idempotent. You press "3rd floor" once, the elevator goes to the 3rd floor. You anxiously press 5 more times, the elevator still only goes to the 3rd floor, not to 5 different 3rd floors.
- **Light switches** are idempotent. The light is already on, you press the switch again, the light stays on, it doesn't become "double bright."
- **Vending machines** are not idempotent. You insert a coin once and get one bottle, insert twice and get two bottles.

For APIs:
- `GET` is naturally idempotent—querying the same resource, no matter how many times, the result is the same, the database doesn't change
- `DELETE` is also idempotent—deleting the same record, the first time succeeds, the second time returns "already doesn't exist," database state unchanged
- `POST` (create) is not idempotent by default—each call creates a new record, which is Xiaoming's problem

### How to Make Create APIs Idempotent?

There are several approaches, choose based on scenario:

**Approach 1: Database unique constraint.** If business-wise "same title + same year" movies shouldn't be duplicated, add a composite unique constraint at the database level. When inserting duplicate data the second time, the database will reject and error, backend catches this error and returns 409 (conflict).

This is the simplest approach, suitable for scenarios with natural unique identifiers. But not all data has natural unique identifiers—for example, comments. The same user can completely post two different comments on the same movie.

**Approach 2: Idempotency Key.** Frontend generates a unique request ID (like UUID) each time it submits, placed in the request header. Backend receives the request and first checks if this ID has been processed—if processed, directly return the previous result; if not, execute normally.

This is the most universal approach, suitable for any scenario. Payment APIs almost all use this method—you definitely don't want users charged twice due to network retries.

**Approach 3: Frontend deduplication + backend safety net.** Frontend disables button + backend adds unique constraint, double insurance. This is enough for most scenarios.

For Xiaoming's movie app, approach 1 is sufficient—the same movie (same title + year) shouldn't be entered twice.

Tell AI:

> "Add a composite unique constraint on title + year for the movies table. If the add movie API encounters duplicate data, return 409 status code with message 'This movie already exists' instead of a 500 error. Also, disable the add button on the frontend after clicking, and restore it when the request returns."

---

## All Errors Show "Something Went Wrong"

Xiaoming's friend gives more feedback: "When I added a movie with the wrong year, the page showed 'Something went wrong.' When I search for a non-existent movie, it also shows 'Something went wrong.' When your server crashes, it also shows 'Something went wrong.' Is it my problem or your problem? Should I retry or change my input?"

Xiaoming checks the code and finds all API error handling looks like this:

```
catch (error) {
  return Response.json({ error: 'Something went wrong' }, { status: 500 })
}
```

No matter what error—wrong parameters, data doesn't exist, database crashed—everything returns 500 and "Something went wrong." The frontend receives this response and can only show a generic error message. Users have no idea what happened or what to do.

### HTTP Status Codes: Error Classification System

You've definitely seen "404 page not found"—clicked an expired link, typed the wrong URL, and the browser shows 404. HTTP status codes are this classification system, 404 is just one of them. You don't need to memorize all status codes, just know the most commonly used ones:

| Status Code | Meaning | When to Use | What Users Should See |
|-------------|---------|-------------|----------------------|
| **200** | Success | Request processed normally | Display data normally |
| **201** | Created | POST created new resource | "Added successfully!" |
| **400** | Bad Request | Parameter validation failed, wrong format | "Title cannot be empty" (specific error message) |
| **404** | Not Found | Queried movie ID doesn't exist | "Cannot find this movie" |
| **409** | Conflict | Duplicate addition of same movie | "This movie already exists" |
| **429** | Too Many Requests | Too many requests in short time | "Too frequent, please try again later" |
| **500** | Server Error | Code bug, database crashed | "Service temporarily unavailable, please try again later" |

Key principle: **400 series is "your (caller's) problem," 500 series is "my (server's) problem"**.

This distinction is very important because it determines how the frontend should handle it:

- **400 series**: User can fix it themselves. Show specific error messages, guide users to correct input. For example "Title cannot be empty"—user fills in the title and can resubmit.
- **500 series**: User can't do anything, can only wait. Just show "Service temporarily unavailable, please try again later." **Don't expose internal error messages to users**—"PostgreSQL connection refused at 10.0.0.5:5432" is useless to users and may leak server internal architecture.

::: danger Don't Expose Internal Details in 500 Errors
This is a security issue. If your 500 error returns database connection strings, SQL statements, file paths, stack traces, etc., attackers can use this information to find weaknesses in your system.

Correct approach: 500 errors only return generic messages ("Service temporarily unavailable"), detailed error information goes to server logs for your own troubleshooting.
:::

### Unified Error Response Format

Consistent with the response format agreed upon in the preface, error responses should also have a unified structure:

<details>
<summary>Response examples for different error types</summary>

**400 Parameter Error:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Title cannot be empty",
    "details": [
      { "field": "title", "message": "Title cannot be empty" },
      { "field": "year", "message": "Year must be integer between 1888-2030" }
    ]
  }
}
```

**404 Resource Not Found:**
```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "Cannot find movie with ID 999"
  }
}
```

**500 Server Error:**
```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "Service temporarily unavailable, please try again later"
  }
}
```

Note that 400 errors include a `details` array listing specific issues for each field. This way the frontend can show error messages next to the corresponding input boxes, rather than just showing a generic "Parameter error" at the top of the page.

</details>

Tell AI:

> "Unify error handling for all endpoints. Return 400 for parameter validation failures with specific error messages for each field. Return 404 for resources not found. Return 409 for duplicate data. Return 500 for internal server errors with only generic messages, log detailed errors. All error responses use unified format `{ success: false, error: { code, message } }`."

---

## "Just Added Successfully, But Not Showing in List"

Xiaoming's friend says: "I just added a movie, the API returned success, but when I go back to the list page and refresh several times, I can't see it. After a while it appeared."

This isn't a bug, it's **cache** at work.

Next.js has multiple caching layers, specific behavior depends on your data fetching method. If the list page is a Server Component directly querying the database, Next.js may cache the entire page rendering result. If the list page calls an API through `fetch`, `fetch` itself also has caching. Either way, the symptom is the same: wrote new data, but reading still gets the old data.

This problem isn't AI writing wrong code, but the framework's caching mechanism at work. The solution is to **proactively tell Next.js "this data changed, please refresh"** after modifying data:

- After successful POST/PATCH/DELETE operations, call `revalidatePath('/movies')` or `revalidateTag('movies')` to notify Next.js to clear corresponding cache
- Next time someone visits this page, Next.js will re-query the database and get the latest data

Tell AI:

> "After adding/modifying/deleting movies, use revalidatePath or revalidateTag to clear related page cache, ensuring users can see the latest data."

::: tip Cache Is Good, But Needs Active Management
Cache can greatly improve performance—the same list page accessed by 100 users only needs one database query, the next 99 times directly return cache. But if not actively cleared, users will see stale data. The principle is: **reads can be cached, writes must clear cache afterward**. Claude Code with `next-best-practices` Skill loaded will automatically add revalidate calls when generating write operation code, but knowing this mechanism helps you quickly locate the cause when problems occur.
:::

---

## Movie Added Successfully, But User Waited Two Seconds

After fixing the cache issue, Xiaoming adds another feature: after users add a movie, send a notification to everyone following that tag.

After the feature goes live, friends report: "Adding movies became slower, after clicking 'Submit' I have to wait two seconds to see 'Added successfully.' It used to be instant."

Xiaoming checks and finds the problem is in the notification logic. The add movie API now does three things:

1. Write movie data to database (tens of milliseconds)
2. Query all users following related tags (hundreds of milliseconds)
3. Send notification to each user (one or two seconds)

These three steps are executed **serially**—step 1 finishes before step 2, step 2 finishes before step 3, all complete before returning "Added successfully." Users have to wait for all notifications to be sent before seeing the result.

But users only care about "was the movie added successfully." Whether notifications were sent, who they were sent to—users don't need to wait for that.

This is the difference between **blocking vs non-blocking**. Database write is the core operation, must wait for it to complete before telling users "success." But sending notifications is a side operation, can completely be done after returning the response—users first see "Added successfully," notifications send slowly in the background.

Next.js provides a feature called `after()` specifically for this: defer operations that don't need to block the response (send notifications, log, update statistics) to execute after the response is sent.

Tell AI:

> "For the add movie API, return success response immediately after database write completes. Operations like sending notifications and updating statistics use Next.js's after() to execute after the response, don't block the user."

::: tip Judgment Criterion: Does This Operation's Result Need to Tell the User?
If yes (like "Added successfully" or "Title cannot be empty"), must complete before response. If no (like send notifications, log, refresh cache), can be placed after response. Claude Code with `next-best-practices` Skill loaded will automatically identify which operations are suitable for `after()`, but when reviewing AI solutions if you find "API became slower," you can think about whether some operations shouldn't block the response.
:::

---

## One Day All APIs Suddenly Return 500

Xiaoming's app ran fine for a month. One evening, he shared the link in his friend group, and dozens of people visited simultaneously. Then all APIs started returning 500 errors.

He throws the error message to AI, which says it's "database connection pool exhausted"—`too many connections`.

### What Is a Connection Pool?

This concept was explained in detail in Chapter 6, Section 6.4. Quick recap:

Every time an API needs to query the database, it first needs to establish a "connection." Establishing connections has cost—requires TCP handshake, authentication, memory allocation, usually takes tens of milliseconds. If every request creates a new connection and discards it after use, dozens of concurrent requests can overwhelm the database.

**Connection pool** pre-creates a batch of connections and puts them in a "pool." When a request comes, borrow one to use, return it when done. Like bike sharing—no need for everyone to buy one, put a batch on the street, people who need them ride away, return them when they arrive.

Xiaoming's problem is: he's using a free database tier with a maximum of only 20 connections. Dozens of people visiting simultaneously, each triggering several requests, connection count instantly maxed out. Later requests all queue up waiting, wait too long and timeout with 500 error.

### How to Troubleshoot and Solve

**Step 1: Confirm if it's a connection pool issue.** Check if error messages contain keywords like `too many connections`, `connection pool exhausted`, `timeout waiting for connection`.

**Step 2: Check connection pool configuration.** Free tier databases typically limit 20-50 connections. How many does your app actually need? Generally, connection pool size set to `CPU cores × 2 + 1` is enough—for most cloud servers, 5-10 connections can handle hundreds of concurrent requests (because each request occupies a connection for a very short time).

**Step 3: Check for connection leaks.** If code has situations where "borrowed connection but didn't return" (like query error but didn't release connection), the connection pool will slowly be exhausted. Like someone riding a bike share but not returning it, bikes become fewer and fewer.

Tell AI:

> "My API reports 'too many connections' error during concurrent access. Help me check: 1) Is database connection pool configuration reasonable? 2) Are there connection leaks (connection not released after query error)? 3) Is connection pool address used instead of direct connection address?"

::: tip Claude Code with Skills Loaded Handles Automatically
If you loaded `supabase-postgres-best-practices` Skill, Claude Code will automatically configure reasonable connection pool parameters, add timeout settings, and ensure connections are properly released when generating database-related code. But understanding this concept is still useful—when problems occur you know which direction to troubleshoot.
:::

---

## Troubleshooting Prompt Templates

When encountering problems after going live, the most efficient troubleshooting method is to throw **complete context** to AI. Vague descriptions only get vague answers.

**When API errors:**
> "My `POST /api/movies` API threw this error: [paste complete error message including stack trace]. The request body is [paste request content]. Help me locate the problem and fix it."

**When data is abnormal:**
> "The movies table in the database has records with empty titles (ID: 42, 43, 47). Normally titles shouldn't be empty. Help me troubleshoot which API didn't do validation causing this, then: 1) Add Zod validation to the API; 2) Clean up these three dirty data records."

**When performance issues:**
> "My movie list API became very slow after data volume reached 5000 records, response time went from 50ms to 3 seconds. Help me analyze the cause—is it missing indexes, N+1 queries, or other issues? Check with EXPLAIN ANALYZE."

**When intermittent issues:**
> "My API is normal most of the time, but occasionally returns 500 errors between 8-10 PM every day. Error message is [paste]. This time period is peak user access. Help me troubleshoot if the connection pool is insufficient."

The key is **give specific data**. Don't say "API is slow," say "response time went from 50ms to 3 seconds." Don't say "sometimes errors," say "errors between 8-10 PM daily, error message is xxx." AI can only give targeted solutions with specific data, otherwise can only give you a bunch of "might be this, might be that" guesses.

---

::: tip Key Takeaways

- **Backend validation is the bottom line**: Frontend validation can't block requests bypassing frontend, backend must validate again
- **Idempotency prevents duplicates**: Database unique constraint is the simplest approach, idempotency key is the most universal approach
- **Status codes distinguish error types**: 400 series is caller's problem, 500 series is server's problem
- **Cache needs active management**: Must clear cache after write operations, otherwise users see stale data
- **Non-core operations don't block response**: Things like sending notifications and logging should be done after response
- **Connection pool prevents crashes**: Dozens of concurrent requests can max out free tier connection count, ensure using connection pool
:::

---

::: info Next Steps
APIs work and can handle load. But as you add more features, you'll find APIs themselves need "management." Go to [Making APIs Better](./03-api-as-product.md) to see how to polish APIs as products.
:::
