---
title: "7.1 One API Isn't Enough"
description: "Join queries, pagination, filtering and sorting—when data volume and requirements grow, how should a simple CRUD interface evolve?"
chapter: "Chapter 7"
---

# 7.1 One API Isn't Enough

> **Goal for this section**: Understand the real-world challenges that emerge when an application moves from "it works" to "it's usable," and learn how to communicate these requirements to AI.

---

## Xiaoming's Movie Detail Page Dilemma

Xiaoming's "Personal Douban" project continues from Chapter 6. The database design is complete—five tables: `movies`, `directors`, `tags`, `movie_tags`, and `ratings`. The relationships are mapped out, constraints are in place. Following the CRUD patterns learned in 7.0, he's got basic create, read, update, and delete operations working—add movies, view the list, edit info, delete records. Everything works.

Xiaoming is thrilled. He figures the backend is pretty much done and starts building the actual pages.

The first page is the movie detail view. He opens Douban to check: a movie detail page displays the title, year, poster, director name, cast list, user rating, tags, and synopsis. In his database, this information is scattered across four or five tables.

What's the most intuitive approach? The frontend fires five requests:

1. `GET /api/movies/1` → Get basic movie info
2. `GET /api/directors/5` → Get director info
3. `GET /api/movies/1/tags` → Get tag list
4. `GET /api/movies/1/ratings` → Get ratings
5. `GET /api/movies/1/actors` → Get cast list

It works. But when Xiaoming opens the page, the experience is terrible—the movie title appears first, the director's name pops in half a second later, then the tags show up after another moment, and the rating loads last. The user sees a "jumping" page, like parts being assembled one by one.

Worse, five requests mean five network round trips. Each round trip takes at least tens of milliseconds, plus server processing time. The user waits several hundred milliseconds to see the complete page. If the user's network is poor (say, on the subway), one request might time out and the page ends up incomplete—title but no director, rating but no tags.

The veteran developer takes one look and asks: "First, figure this out—how is this page being rendered?"

### First Question: Does This Page Even Need an API?

This is a question many beginners overlook. In Next.js, page components (`page.tsx`) run on the server by default and can **query the database directly**—no need to go through an API round trip.

If Xiaoming's movie detail page is a Server Component (which it is by default), it can call Drizzle queries directly in the component, fetch data from all five tables at once, and render HTML to send to the browser. The entire process involves zero "frontend sends request → backend returns" network round trips—the data is already on the server.

So who are API Routes for? Two scenarios:

- **When the frontend needs dynamic interaction**—like when a user clicks a "Favorite" button, the frontend needs to tell the backend "I want to favorite this movie." These user-triggered write operations need some way to call backend logic
- **External consumers**—like if Old Wang wants to use Xiaoming's data for a mini-program, he needs a callable HTTP interface

Xiaoming's detail page is "show data when the page opens," no user interaction required. So the veteran says: "Just query the database directly in the Server Component for the detail page. No need to write an API Route."

::: tip Server Actions: Write Operations Without Writing Interfaces
You might notice that AI-generated "favorite," "like," or "submit form" features don't have `app/api/xxx/route.ts` files. Instead, there's a function marked with `'use server'`—this is a **Server Action**.

Server Actions let the frontend call a server-side function directly, without manually defining API routes or writing `fetch` requests. For simple write operations like "user clicks button → backend processes → returns result," they're cleaner than API Routes.

You don't need to specify which approach to use. Claude Code with the `next-best-practices` Skill loaded will automatically decide—page display uses Server Component direct database queries, simple write operations use Server Actions, and interfaces for external consumption use Route Handlers. Don't be surprised if you don't see `route.ts` in the code; the AI probably chose a more suitable approach.
:::

However, Xiaoming's movie list page has filtering, sorting, and pagination—these are user-triggered interactions where the frontend needs to dynamically request different data based on user actions. This scenario does need an API.

So the pagination, filtering, and sorting discussed next all apply to **scenarios that need an API**—where the frontend dynamically requests data based on user actions.

But even in API scenarios, the "one page firing five requests" problem still exists. For example, when Xiaoming later adds "favorite" and "comment" features to the movie detail page, it becomes a client-side interactive page that needs to fetch data via API. At this point, how should the data structure be organized?

---

## Nested vs. Flat: Two Data Organization Approaches

Suppose the frontend needs movie info and director info. The backend can return this in two ways:

### Nested Structure

Embed related data "inside" the main object, returning all information in one go:

<details>
<summary>What does a nested response look like? Expand to see</summary>

```json
{
  "id": 1,
  "title": "Spirited Away",
  "year": 2001,
  "director": {
    "id": 5,
    "name": "Hayao Miyazaki",
    "nationality": "Japanese"
  },
  "tags": ["Animation", "Fantasy", "Adventure"],
  "rating": {
    "average": 9.4,
    "count": 2156
  }
}
```

The frontend can directly use `movie.director.name` to get the director's name and `movie.rating.average` to get the rating—no need for second or third requests. All data arrives at once.

</details>

### Flat Structure

Only return the IDs of related data; the frontend fetches more if needed:

<details>
<summary>What does a flat response look like? Expand to see</summary>

```json
{
  "id": 1,
  "title": "Spirited Away",
  "year": 2001,
  "directorId": 5,
  "tagIds": [1, 3, 7],
  "averageRating": 9.4
}
```

After getting `directorId: 5`, if the frontend needs to display the director's name, it must call `GET /api/directors/5`. To display tag names, it needs to query the tags table with `tagIds`.

</details>

### How to Choose?

This isn't an either/or choice—it depends on the scenario:

| Scenario | Recommended Structure | Reason |
|----------|----------------------|--------|
| Movie detail page | Nested | The page needs to display complete info; fetching everything at once saves the frontend multiple trips |
| Movie list page | Flat (or lightly nested) | Lists only need title, year, poster; including full director info and all tags wastes bandwidth |
| Admin dashboard tables | Flat | Tables show only key fields per row; load details when a row is clicked |
| Search results | Flat + minimal nesting | Search results need title and rating, but not full director biographies |

A practical criterion: **After receiving the data, does the frontend need to make additional requests to render the page?** If yes, your interface isn't returning enough data; nest more related data. If the frontend can render directly with the received data, it's just right.

Xiaoming changed his movie detail API to a nested structure, returning all info in one request. The page went from "parts assembly" to "one-shot completion"—faster loading, better experience.

When talking to AI, just describe your needs directly:

> "The movie detail API needs to return director info, tag list, and average rating simultaneously, using a nested structure with all data in one request. The movie list API only returns title, year, poster URL, and average rating."

---

<ApiEvolution />

## 500 Records Returned at Once, Page Freezes

With the detail page problem solved, Xiaoming starts working on the homepage movie list.

At first there were only a dozen or so movies; `GET /api/movies` returned an array, the frontend looped through and rendered them in milliseconds. Smooth experience.

Then Xiaoming spent a weekend entering every movie he'd ever watched—over 500. Opening the homepage again, the browser spun for several seconds before displaying. He opened dev tools: the API returned a massive JSON with 500+ records, several hundred KB of data. The frontend had to render 500+ movie cards, thousands of DOM nodes, and the browser froze.

It's like going to a library and asking the librarian "bring out all your books for me to look at." The librarian spends the afternoon moving tens of thousands of books in front of you—you can't possibly look through them all. The normal approach is: "Show me sci-fi books first, 20 at a time, then bring the next batch when I'm done."

This is **pagination**.

### Offset Pagination: Page-Flipping Mode

The most common pagination method is **offset pagination**—telling the backend "skip the first N records, give me the next M."

Request: `GET /api/movies?page=3&limit=20`

Meaning: Page 3, 20 items per page. The backend skips the first 40 (pages 1 and 2), returning records 41-60.

The advantage is **simple and intuitive**. The frontend can directly display page number navigation: "Page 1, Page 2, Page 3..." Users can click any page to jump. The pagination bars at the bottom of Taobao and JD.com search results use this approach.

The downside is **gets slower as you go deeper**. When the database executes `OFFSET 10000 LIMIT 20`, it must scan the first 10,000 records (though not return them) before fetching your 20. Like finding book 500 in a library—the librarian counts from book 1 to 500, then hands you the next 20. Counting from the start every time, the deeper the page, the slower it gets.

This issue was covered in Chapter 6, Section 6.3 on OFFSET pagination pitfalls. For Xiaoming's 500 movies, offset pagination is perfectly adequate—performance issues only become noticeable with tens of thousands of records.

### Cursor Pagination: Bookmark Mode

Another approach is **cursor-based pagination**—no page numbers, just "bookmarks."

Request: `GET /api/movies?cursor=eyJpZCI6NDB9&limit=20`

Meaning: From after the last record returned previously, give me 20 more. `cursor` is an encoded marker pointing to the last record's position on the previous page.

This is like placing a bookmark in the library. Next time, start fetching from the bookmark position without counting from the beginning. No matter how deep you go, speed stays constant—the database reads directly from the bookmark without scanning previous records.

The downside is **can't jump pages**. You can only go "next, next" page by page; can't directly jump to page 50. This is why social media feeds (Weibo, Moments, TikTok) use infinite scroll instead of pagination—they use cursor pagination.

### How to Choose?

| Scenario | Recommended Approach | Reason |
|----------|---------------------|--------|
| Admin dashboard tables | Offset pagination | Needs page navigation, data volume usually manageable |
| Product search results | Offset pagination | Users expect pagination, need to jump to specific pages |
| Social feed / comment lists | Cursor pagination | Infinite scroll, large data volume, no need to jump pages |
| Data volume < 10,000 | Offset pagination | Simple and sufficient, performance not an issue |
| Data volume > 100,000 | Cursor pagination | Offset pagination gets slower with depth |

Xiaoming's movie collection only has a few hundred titles; offset pagination is more than enough.

Tell the AI:

> "Add pagination to the movie list API using offset/limit, default 20 per page. Include total count and total pages in the response for frontend page navigation."

### What Should a Paginated Response Include?

Returning just the data isn't enough—the frontend also needs to know "how many total," "what page am I on," "is there a next page"—otherwise it doesn't know how many page buttons to show or whether to disable the "next page" button.

A good paginated response looks like this:

<details>
<summary>Paginated response example</summary>

```json
{
  "success": true,
  "data": [
    { "id": 1, "title": "Spirited Away", "year": 2001, "rating": 9.4 },
    { "id": 2, "title": "My Neighbor Totoro", "year": 1988, "rating": 9.2 },
    { "id": 3, "title": "Castle in the Sky", "year": 1986, "rating": 9.1 }
  ],
  "meta": {
    "total": 500,
    "page": 1,
    "limit": 20,
    "totalPages": 25
  }
}
```

The `meta` info tells the frontend: 500 total records, currently on page 1, 20 per page, 25 pages total. The frontend renders the pagination bar accordingly and disables the "next page" button on the last page.

</details>

::: tip Cursor pagination responses differ slightly
Cursor pagination doesn't return `total` and `totalPages` (because calculating the total itself requires a full table scan, which is slow). Instead, it returns a `nextCursor`. The frontend uses this cursor to request the next page; if `nextCursor` is empty, there are no more records.
:::

---

## Want to Filter by Tag, Sort by Rating

Pagination solved, Xiaoming has new requirements.

His friend tries it out and says: "Can your movie list show only animated films? I don't want to page through 500 movies to find them." Another friend says: "Can you sort by rating highest first? I want to see your highest-rated movies."

Xiaoming struggles: Should he create a new API for each filter condition? `GET /api/movies/animation` returns animated films, `GET /api/movies/top-rated` returns highly-rated movies? But what if you want both filter by tag AND sort by rating? Another `GET /api/movies/animation/top-rated`? The combinatorial explosion of interfaces would be unmanageable.

The veteran says: "Don't create new interfaces. One list API with query parameter combinations."

### The Art of Query Parameter Combinations

`GET /api/movies?tag=Animation&sort=rating&order=desc&page=1&limit=20`

This single request expresses: "Give me movies tagged 'Animation,' sorted by rating high to low, page 1, 20 per page."

Query parameters are great because they **combine freely**, like LEGO bricks:

- Just want to sort, no filter? `GET /api/movies?sort=rating&order=desc`
- Just want to filter, no sort? `GET /api/movies?tag=Animation`
- Want to filter by multiple tags? `GET /api/movies?tag=Animation&tag=Japanese`
- Pass nothing? `GET /api/movies` returns all data in default order (with pagination)

Only one interface; the frontend constructs different parameters based on user actions. User selects "Animation" in the filter panel, frontend adds `tag=Animation`; user clicks "Sort by rating," frontend adds `sort=rating&order=desc`. The interface code doesn't change—all combinations are automatically supported.

### Common Query Parameter Designs

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `page` / `limit` | Pagination | `?page=2&limit=20` |
| `sort` / `order` | Sorting | `?sort=rating&order=desc` |
| `tag` / `genre` | Filter by category | `?tag=Sci-Fi` |
| `year` | Filter by year | `?year=2024` or `?yearFrom=2020&yearTo=2024` |
| `q` / `search` | Keyword search | `?q=Spirited Away` |
| `minRating` | Minimum rating | `?minRating=8` |

All these parameters should be **optional**. If not provided, use defaults—no filter by default, sort by creation time descending by default, page 1 with 20 per page by default. This provides flexibility without breaking existing calls.

::: warning Search and filter are different things
**Filter** is exact match—"tag equals Animation," "year equals 2024." **Search** is fuzzy match—"title contains 'Spirited Away'." They can be combined but are implemented differently. Filters use database WHERE conditions; search may need full-text indexes.

For Xiaoming's movie collection, simple `LIKE '%keyword%'` search is sufficient. If data grows to hundreds of thousands, PostgreSQL's full-text search might be needed—but that's for later; get it working first, optimize later.
:::

Tell the AI:

> "The movie list API supports these optional query parameters: tag (filter by tag), year (filter by year), q (search by title), sort (sort field, supports rating/year/createdAt), order (asc or desc). All parameters are optional; if not provided, returns all data in default order."

---

## Xiaoming's API Evolution Journey

Let's review how Xiaoming's movie list API evolved step by step:

| Stage | API | Problem |
|-------|-----|---------|
| V1 | `GET /api/movies` → returns all 500 records | Page freezes |
| V2 | `GET /api/movies?page=1&limit=20` → paginated | Can't find desired movies |
| V3 | `GET /api/movies?tag=Animation&sort=rating&order=desc&page=1&limit=20` | Good enough! |

Each evolution was driven by real needs—not pre-designed by Xiaoming, but discovered through usage and then enhanced. This is the norm for API design: start with the simplest version, iterate when problems arise. No need to design a "perfect" API from the start.

---

## Prompt Templates for Communicating with AI

Combine the concepts learned above to describe a complete API requirement in one paragraph:

**When designing an API from scratch:**

> "Help me design two APIs: movie list and detail. The list API should support pagination (offset/limit, default 20 per page), filter by tag, sort by rating or year, and include total count and total pages in the response. The detail API returns movie info with nested director info, tag list, and average rating. Use `{ success, data, error }` format for all responses."

**When adding features to an existing API:**

> "The current `GET /api/movies` only returns all data. Add pagination and filtering. Pagination uses query parameters page and limit; filtering supports tag and year; sorting supports sort and order parameters. All new parameters are optional—when not provided, behavior remains the same as before, maintaining backward compatibility."

**When having AI self-review:**

> "Check the existing movie list API for these issues: 1) Does it support pagination? 2) Are query parameters type-validated (e.g., page must be positive integer)? 3) Are sort fields restricted to allowed values (preventing users from passing arbitrary column names)?"

---

::: tip Let AI Automatically Optimize Performance
As your API code grows more complex, you might unknowingly introduce performance issues—like data waterfalls (one request waiting for another), unnecessary re-renders, oversized bundles, etc.

Recommend loading these Skills so AI automatically follows best practices when writing code:

- **`vercel-react-best-practices`** - React/Next.js performance optimization (eliminate waterfalls, bundle optimization, re-render optimization)
- **`next-best-practices`** - Next.js file conventions, RSC boundaries, async APIs, route handling, metadata

These Skills are usually built into Claude Code. They automatically load when you use React or Next.js, helping you avoid common performance pitfalls.
:::

---

::: info Next Step
The API can query and filter now, but new issues will arise after launch—empty submissions, duplicate clicks, sudden 500 errors. Go to [When Things Go Wrong](./02-when-things-go-wrong.md) to see how to handle them.
:::