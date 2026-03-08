---
title: "6.1 Data Storage Evolution"
description: "The evolution path from CSV to JSON to databases"
---

# 6.1 Data Storage Evolution

> **Goal of this section**: Understand the evolution of data storage methods, and know when to use a database.

---

## Xiaoming's Douban Movie Dream

Starting from this section, we'll follow an indie developer named Xiaoming through the complete journey from database to product launch. Like you, he writes code with Claude Code, and the pitfalls he encounters are pretty much the same ones you'll run into.

Xiaoming is a hardcore movie buff. He watches at least three films every week, from blockbuster hits to niche arthouse films, from Hollywood to Japanese, Korean, and European cinema—he takes them all. He has a habit: after finishing each movie, he jots down a few thoughts in his phone's notes app.

Over time, his notes accumulated hundreds of scattered viewing records, making them extremely painful to search through. When he wants to find "which sci-fi movie was the best among last year's watches," he has to scroll through them one by one.

So Xiaoming decided to build a "Personal Douban"—his own movie database that can record every film he's watched, with ratings, short reviews, tags, and the ability to filter and analyze by various dimensions.

At first, he thought it would be simple: just open Excel, right?

So he opened Excel and created a table:

| Movie Title | Director | Rating | Tags | Year | Review |
|-------------|----------|--------|------|------|--------|
| The Wandering Earth | Guo Fan | 7.9 | Sci-Fi | 2019 | Mind-blowing effects |
| Ne Zha | Jiaozi | 8.4 | Animation | 2019 | Laughs and tears |
| Farewell My Concubine | Chen Kaige | 9.6 | Drama | 1993 | Timeless classic |

For the first 20 movies, everything went smoothly. There weren't many columns, and management was easy.

But Xiaoming is a serious film buff, and he quickly recorded over 200 films. Problems started popping up one after another—and each one was more headache-inducing than the last.

---

## Stage 1: CSV / Excel — It Works, But Quickly Becomes Insufficient

Excel and CSV are where most people start with data management. CSV (Comma-Separated Values) is essentially a plain-text version of Excel—one record per line, fields separated by commas. You can open it with Notepad, and any programming language can easily read and write it:

```csv
Movie Title,Director,Rating,Tags,Year
The Wandering Earth,Guo Fan,7.9,Sci-Fi,2019
Ne Zha,Jiaozi,8.4,Animation,2019
Farewell My Concubine,Chen Kaige,9.6,Drama,1993
```

The problems Xiaoming encountered:

**Problem 1: What about multiple tags for one movie?**

*The Wandering Earth* is "Sci-Fi," "Disaster," and "Domestic." In CSV, the tags column can only be written as `Sci-Fi/Disaster/Domestic`, mashed together with slashes. Want to find "all sci-fi movies"? You have to use string matching, which is slow and error-prone—"Sci-Fi Film" won't match "Sci-Fi," and "Hard Sci-Fi" won't match either.

The essence of this problem is: **each cell in CSV can only hold one value**. When a field needs to store multiple values (multiple tags, multiple actors), CSV falls short. You can only cram multiple values into one cell, then use various string tricks to split them apart during queries—this is both fragile and inefficient.

**Problem 2: Data inconsistency**

Xiaoming sometimes writes "Guo Fan," sometimes "Director Guo Fan," and sometimes slips and writes "Guo Fan" with the wrong character. Among 200 records, the same director might have three or four different variations. Excel won't warn you—wrong is wrong.

When Xiaoming wants to calculate "the average rating of Director Guo Fan's works," he finds only one film in the results—because the director names were written incorrectly for the other two. This kind of error is invisible with small datasets, but becomes a nightmare as data grows. You don't even know how much data is wrong, because there's no mechanism to help you check.

**Problem 3: Related data doesn't fit**

Xiaoming wants to add a feature: recording the cast list for each movie. A movie has a dozen actors, and an actor has appeared in dozens of movies. This "many-to-many" relationship simply can't fit in one Excel sheet.

If forced, either each row becomes super long (all actor names concatenated with commas), or you have to create multiple sheets and manually cross-reference—"which person does this actor ID correspond to again?" Every query requires jumping between multiple sheets, relying entirely on human brainpower to make associations, which is extremely inefficient and error-prone.

**Problem 4: Multi-user collaboration is a disaster**

Xiaoming wants friends to help maintain the list. Two people editing the same Excel file simultaneously? Either one overwrites the other's changes, or conflict files are created. Google Sheets allows multiple people to edit at once, but it's still fundamentally a spreadsheet—none of the previous three problems are solved.

> What is CSV/Excel good for? One-time data analysis, simple configuration lists, small datasets of a few dozen rows. Once data volume grows, relationships become complex, or multi-user collaboration is needed, it's time to switch solutions.

---

## Stage 2: JSON Files — The Programmer's Instinctive Choice

After learning some programming, Xiaoming discovered the JSON format. JSON (JavaScript Object Notation) is the most common data format in web development—you've already encountered `package.json` and `tsconfig.json` in previous chapters.

JSON is much more flexible than CSV. Its biggest advantage is support for **nested structures** and **arrays**. The "multiple tags for one movie" problem that stumped Xiaoming in CSV is easily solved in JSON:

```json
{
  "movies": [
    {
      "id": 1,
      "title": "The Wandering Earth",
      "director": "Guo Fan",
      "rating": 7.9,
      "tags": ["Sci-Fi", "Disaster", "Domestic"],
      "review": "Mind-blowing effects",
      "actors": ["Wu Jing", "Qu Chuxiao", "Li Guangjie"]
    }
  ]
}
```

Tags and actors can now use arrays, and nested structures can be expressed. Xiaoming is thrilled, thinking he's finally found the perfect solution.

But when he turns his app into a web version and starts having users access it, new problems emerge:

**Problem 1: Low query efficiency**

To find "all sci-fi movies rated 8 or above," the program needs to read the entire JSON file into memory, then iterate through it record by record, checking each record's `rating` and `tags`. With small data volumes this is unnoticeable, but when Xiaoming's movie library grows to tens of thousands of entries, every query requires scanning the entire file, and page loads become noticeably slower.

Databases don't have this problem—they have **indexing** mechanisms that can jump directly to data matching the conditions, without scanning record by record. This is like searching for a keyword in a book: without a table of contents you have to flip from beginning to end, but with a table of contents you can jump directly to the relevant page.

**Problem 2: No data constraints**

JSON files won't prevent you from writing incorrect data. Rating written as `"very high"` instead of a number? Actor ID pointing to a non-existent person? The file won't throw an error—the data just quietly becomes corrupted. You might not discover some data is wrong until months later, and by then you won't know when or by whom it was written incorrectly.

Databases have **constraint mechanisms**—you can define "rating must be a number between 0-10," "actor ID must point to a real existing actor," and data violating these rules simply can't be written in.

**Problem 3: Concurrent writes lose data**

Two requests simultaneously read the JSON file, each modifies it, and each writes it back—the second write overwrites the first one's changes. This is fatal in web applications. Imagine two users simultaneously rating different movies, and only one person's rating is saved while the other's modification vanishes into thin air.

This problem is called a **Race Condition**—have you ever tried to grab the last item in stock during a big e-commerce sale? Two people click "Buy Now" at the same time, and who succeeds depends on whose request reaches the server first—the result is unpredictable. This is one of the most classic bugs in concurrent programming. Databases fundamentally solve this through **transactions and locking mechanisms**—transactions ensure "a group of operations either all succeed or all rollback," and locks ensure "only one person can modify this data at a time."

**Problem 4: Join queries are painful**

To find "the average rating of all movies starring Wu Jing," you need to iterate through all movies, check if "Wu Jing" is in the actors array, collect the ratings, and calculate the average. The code is long and slow. If data is scattered across multiple JSON files (one file for movies, one for actors), joining them is even more troublesome—you have to write your own code to "stitch" the data from two files together.

Databases have dedicated **JOIN** operations for this. A single statement can associate data from multiple tables, and the database engine automatically optimizes the query path, making it orders of magnitude faster than your hand-written iteration code.

> What is JSON good for? Application configuration files (`package.json`, `tsconfig.json`), API data transfer formats, small amounts of static data. But as persistent storage for business data, it can't hold up.

---

## Stage 3: Database — Leave Professional Work to Professional Tools

After stumbling through CSV and JSON, Xiaoming finally decides to use a database. He chooses PostgreSQL (the reasons were covered in section 6.0), and splits his data into several tables:

- `movies` table: stores basic movie information (title, year, rating, synopsis)
- `directors` table: stores director information (name, nationality)
- `actors` table: stores actor information (name, nationality)
- `movie_actors` table: records which actor appeared in which movie (junction table, solving many-to-many relationships)
- `tags` table: stores tags (Sci-Fi, Action, Arthouse...)
- `movie_tags` table: records which movies have which tags (another junction table)

You might ask: why split into so many tables? Can't we just use one big table?

No. One big table means massive data redundancy. For example, the actor "Wu Jing" appears in 20 movies. With one table, "Wu Jing's" name, nationality, and other information would be duplicated 20 times. Change his information, and you have to change 20 places—miss one, and you have data inconsistency. After splitting into a separate `actors` table, "Wu Jing" is stored in only one record, and all movies reference it by ID. Change it in one place, and it takes effect everywhere.

This is the core idea of relational databases: **eliminate data redundancy and ensure data consistency through table splitting and relationships.**

Now, all previous problems are solved:

| Previous Problem | How Database Solves It |
|-----------------|------------------------|
| Multiple tags don't fit | Split into independent tags table + junction table, clean and elegant |
| Data inconsistency | Directors are in an independent table, reference by ID instead of handwritten names |
| Slow join queries | SQL JOIN operations, internally optimized by database, millisecond-level |
| Multi-user collaboration conflicts | Transaction mechanism + row-level locking, concurrency-safe |
| No constraints | Primary keys, foreign keys, NOT NULL, UNIQUE, automatically intercept dirty data |
| Low query efficiency | Index acceleration, millions of records can still be queried in seconds |

Xiaoming can find "all movies rated 8 or above starring Wu Jing" with a single SQL query:

```sql
SELECT m.title, m.rating
FROM movies m
JOIN movie_actors ma ON m.id = ma.movie_id
JOIN actors a ON ma.actor_id = a.id
WHERE a.name = 'Wu Jing' AND m.rating >= 8.0
```

You don't need to understand this SQL syntax—AI will write it for you. But you need to understand what it's doing: from three tables (movies, junction table, actors), finding matching data by associating through IDs. This is the power of relational databases: **data is stored separately, dynamically joined during queries.**

This query can return results in milliseconds even among millions of records—because the database automatically uses indexes to optimize the query path. The same operation with JSON files might take seconds or even tens of seconds.

---

## Evolution Pattern: When to Upgrade?

Looking back at Xiaoming's journey, you'll discover a clear pattern: **it's not that the storage tool is bad, but that business complexity has exceeded the tool's capacity.**

CSV works perfectly at 20 records, starts struggling at 200, and completely collapses at 2,000. JSON solves the flexibility problem for structured data, but hits walls with concurrency and query efficiency. Every upgrade happens because of problems that the current solution can't solve.

Not all scenarios need a database. The key to choosing a storage method is understanding how complex your data is:

| Signal | Explanation | What to Use |
|--------|-------------|-------------|
| Less than 100 records, simple structure | Configuration files, static lists | CSV / JSON files |
| Data has relationships | User → Order → Product | Relational database |
| Multiple people need to read/write simultaneously | Web applications, API services | Database |
| Need to ensure data correctness | Amounts, inventory, user information | Database (with constraints) |
| Data volume exceeds a few thousand | Query performance starts degrading | Database (with indexes) |
| Need complex queries | Statistics, sorting, grouping, joining | Database (SQL) |

A simple rule of thumb: **if your application has the concept of "users," you should use a database.** Because with users comes registration and login, with login comes sessions, with sessions comes permissions, with permissions come roles... these relationships can only be handled elegantly by a database.

<StorageEvolution />

Conversely, if you're just building a static website, a tool page that doesn't require user login, or a purely frontend calculator, you don't need a database at all. Store data in the browser's `localStorage`, or hardcode it in your code.

---

::: tip Key Takeaways
CSV/Excel → JSON → Database is not "the more advanced the better," but "business complexity determines storage method." Each solution has its optimal use case. The key is recognizing how complex your data is, whether there are relationships, and whether multi-user concurrent access is needed. Your Vibe Coding project will likely need a database—we'll learn core database concepts in the next section.
:::