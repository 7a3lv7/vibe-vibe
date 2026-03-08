---
title: "6.3 How to Work with Databases"
description: "Understanding ORM tools, mastering CRUD operations, and learning how to review AI-generated database code"
---

# 6.3 How to Work with Databases

> **Goal for this section**: Understand core database concepts (CRUD, ORM, transactions), and learn to review AI-generated database code.

---

## Xiaohong's First Requirement

In the previous section, Xiaohong designed the table structure for the campus food delivery platform. The tables are ready, and the relationships are clear, but the database is still empty—like a building that's been constructed but has no occupants yet.

Now she faces a practical problem: how do you store and retrieve data in the database?

Her first requirement is simple: user registration. A student opens the app, enters their phone number and nickname, and taps "Register"—the backend needs to store this user information in the `users` table.

This operation is called an **Insert**, one of the most fundamental database operations. It sounds straightforward, but Xiaohong immediately faces a choice: should she write SQL statements directly, or use something else?

She chose to use a tool called **ORM**.

---

## ORM: The Translator Between Code and Database

**ORM (Object-Relational Mapping)** is a "translation layer" that lets you operate the database using a programming language (TypeScript/JavaScript) without writing SQL by hand.

Think of ORM as a translator: you speak Chinese (TypeScript code), the database only understands English (SQL), and ORM handles the translation in between. You write `db.insert(users).values({ phone: '138xxxx', name: 'Xiaohong' })`, and ORM translates it to `INSERT INTO users (phone, name) VALUES ('138xxxx', 'Xiaohong')` and sends it to the database for execution.

Why do you need this translator? Can't you just write SQL directly?

**Problems with writing SQL directly.** SQL is the database's "native language"—powerful, but with several pain points: string concatenation is error-prone, there's no type checking (a misspelled column name only surfaces at runtime), and SQL syntax varies across different databases. For Vibe Coders, the biggest issue is that when you ask AI to generate SQL, it's hard to tell at a glance whether it's correct. A complex SQL statement might span a dozen lines with three or four levels of nesting—just matching the parentheses can be dizzying.

**Benefits of ORM.** ORM maps database tables to objects in your code and SQL operations to function calls. Column names have autocomplete, types are checked, and mistakes show up as red squiggles in your editor. Plus, ORM-generated code is easier to read and review—you see `db.select().from(users).where(eq(users.phone, '138xxxx'))`, and even without knowing SQL, you can guess what it's doing.

This tutorial recommends **Drizzle ORM** for these reasons:

- Syntax stays close to SQL—if you know SQL concepts, you can read it
- Type-safe TypeScript makes AI-generated code easier to review
- Supports both PostgreSQL and SQLite, making it easy to switch
- Active community with comprehensive documentation

Another popular choice is **Prisma**, which uses its own Schema language to define table structures—cleaner syntax but further from SQL. Both are excellent tools; pick one and stick with it. What matters isn't which you choose, but understanding the concepts behind ORM—because concepts are universal, tools can be swapped.

---

## CRUD: The Four Basic Database Operations

Whether you're building a food delivery platform, social app, e-commerce site, or blog system, all database operations boil down to four types, collectively known as **CRUD**:

| Operation | English | Meaning | Real-World Analogy |
|-----------|---------|---------|-------------------|
| **C**reate | Create | Insert new data into a table | New user registration, placing a new order |
| **R**ead | Read | Query data from a table | Viewing order list, searching products |
| **U**pdate | Update | Modify existing data | Changing delivery address, updating order status |
| **D**elete | Delete | Remove data | Canceling an order, deleting an account |

The CRUD acronym is extremely common in developer circles. When someone says "this feature is just CRUD," they mean "the core of this feature is basic database operations—no complex business logic." In fact, 80% of most web applications are CRUD—the truly complex business logic is only a small fraction.

These four operations cover 99% of business scenarios. Any feature you see in any application, when broken down to the database level, is a combination of CRUD.

For example, in Xiaohong's food delivery platform:

- User registration → **Create** a users record
- Browsing the menu → **Read** from the dishes table, filtered by restaurant
- Placing an order → **Create** an orders record + multiple order_items records
- Changing address → **Update** the address field in the users table
- Canceling an order → **Update** the orders status to "canceled" (usually not actually deleted)
- Restaurant removing a dish → **Delete** or **Update** the dishes status

Note the last point: in real projects, data is rarely actually deleted. The more common approach is **"soft delete"**—adding a `deleted_at` field to mark as deleted while keeping the data. This allows recovery if deleted by mistake and facilitates data analysis.

Why not delete directly? Imagine: a user cancels an order, and you permanently delete the order record from the database. A month later, the user complains "I clearly placed an order, why is there no record?" What do you use to prove it? Soft delete is like adding a "recycle bin" to your data—marked as deleted, but still there, ready to be restored or verified when needed.

<CRUDVisualizer mode="crud" />

---

## Reviewing AI-Generated CRUD Code

As a Vibe Coder, you don't need to write CRUD code yourself—AI will write it for you. But you need to know how to review it.

It's like hiring a renovation worker. You don't need to paint walls or lay flooring yourself, but you need to know if the paint is smooth and the flooring is straight. If you know nothing about renovation, you won't spot corner-cutting.

AI-generated database code is the same. It's mostly correct, but occasionally makes "looks like it runs but actually has hidden issues" mistakes. Here are the review points for each operation:

**Create (Insert) Review Checklist:**

- Are all required fields provided? (NOT NULL columns cannot be omitted)
- Is there input validation? (phone number format, nickname length)
- Is there error handling for insert failures? (e.g., duplicate phone number triggers UNIQUE constraint violation)

**Read (Query) Review Checklist:**

- Are the query conditions correct? (querying by `user_id` vs. `phone`)
- Is a join needed? (when querying orders, do you need user info too)
- Is there pagination? (without pagination, large datasets return tens of thousands of rows at once, freezing the page)
- Is there sorting? (usually by creation time descending, newest first)
- **Is there an N+1 problem?** (see explanation below)

**Update Review Checklist:**

- Is the WHERE clause correct? (without WHERE, all rows in the entire table are updated!)
- Are only the necessary fields being updated? (don't overwrite other fields)
- Is the `updated_at` timestamp being updated?

**Delete Review Checklist:**

- Is it a hard delete or soft delete? (soft delete recommended for production)
- Is the WHERE clause correct? (without WHERE, the entire table is deleted!)
- Are associated data considered? (what happens to a user's orders before deleting the user?)

::: danger WHERE Clause is the Lifeline
Update and Delete operations without a WHERE clause affect **all rows** in the table. This is the most common and most fatal error in database operations.

Imagine you want to change Xiaohong's address to "Building 3, Room 201," but the AI-generated code forgot the WHERE clause—the address of **every user** in the entire `users` table is changed to "Building 3, Room 201." Thousands of users' addresses, changed in an instant.

When reviewing AI code, the first thing to check when you see UPDATE or DELETE is whether there's a WHERE clause. An UPDATE/DELETE without WHERE is almost always a bug.
:::

---

## Common Performance Traps

Code that runs and code that runs fast are two different things. Here are the three most common performance traps in real projects—whether written by humans or AI, they can be hit once data volume grows. The good news is, you only need to know the names of these pitfalls; when you encounter performance issues, just tell AI "check for N+1 queries."

### N+1 Query: The Most Insidious Performance Killer

Xiaohong is building a "merchant dashboard" page showing all users and their respective order counts.

A common incorrect approach: first query all users (1 query), then **query each user's orders separately** (N queries). With 100 users, that's 101 database queries—this is the N+1 problem.

Why is it called N+1? Because the first query returns N records, then 1 additional query is made for each record, totaling N+1 queries.

This problem is particularly insidious because in development environments (small datasets, low network latency), it's barely noticeable. But in production, every database query has network round-trip overhead (typically 1-5ms). 100 queries means 100-500ms, plus database processing time, easily pushing page load to several seconds.

The correct approach is to use JOIN or batch queries to fetch all data at once. When reviewing, if you see "database queries inside a loop," it's almost certainly N+1.

N+1 can turn a 100ms page load into 10 seconds. If you find a page inexplicably slow, this is your first suspect.

<CRUDVisualizer mode="n1" />

### Pagination Gets Slower: The OFFSET Trap

Xiaohong's dish list has thousands of items and needs pagination—20 per page, with users browsing through pages. AI typically generates pagination using OFFSET—"skip the first N items, take the next 20."

The first few pages are fast, but page 50 is noticeably slower. Why?

Imagine finding the 1000th book in a library. The OFFSET approach is: start from the first book, count to the 1000th, then take the next 20. Every page turn starts counting from the beginning—the deeper the page, the more counting, the slower it gets.

Databases work the same way. `OFFSET 1000 LIMIT 20` means the database must scan the 1000 skipped rows before fetching your 20. These skipped rows aren't returned to you, but the database still has to read and process them.

A better approach is **Cursor Pagination**: remember the ID of the last record on the previous page, and start from after that ID for the next page. Like placing a bookmark in the library—next time start from the bookmark, no need to count from the beginning. No matter which page you're on, the speed stays consistent.

When reviewing pagination code, seeing `OFFSET` should raise a flag—small data volumes are fine, but large datasets absolutely need cursor pagination.

<CRUDVisualizer mode="paging" />

### Check-Then-Update Concurrency Trap

Xiaohong is building a "user settings" feature. When users change their theme color, the setting might already exist (needs update) or be the first time (needs insert).

AI might generate "first check if it exists, update if yes, insert if no" logic. This works fine with a single user. But imagine this scenario: the user rapidly double-taps the "Save" button, and two requests arrive at the server almost simultaneously.

First request checks: "setting doesn't exist." Second request also checks: "setting doesn't exist." Then both requests try to insert—the second one fails with a primary key or unique constraint violation.

This is called a **race condition**, mentioned in section 6.1 when discussing JSON concurrent writes. Databases have a specific operation for this called **UPSERT** (INSERT ... ON CONFLICT DO UPDATE)—one atomic statement that "updates if exists, inserts if not," with no concurrency conflicts.

When reviewing, seeing the "first SELECT then decide INSERT or UPDATE" pattern should be changed to UPSERT.

---

## Transactions: All or Nothing

On Xiaohong's platform, when a user places an order, three things must happen simultaneously:

1. Create an order record
2. Create multiple order item records (which dishes were ordered)
3. Deduct inventory for the corresponding dishes

If step 1 succeeds, step 2 succeeds, but step 3 finds insufficient inventory—what then? The order is already created, but the dish has no inventory. This is **data inconsistency**. The user thinks the order succeeded, the merchant sees negative inventory, and both are confused.

**Transactions** solve this problem. A transaction packages multiple operations into an "atomic operation": either all succeed, or all rollback (undo), with no "half-done" intermediate state.

The word "atomic" comes from physics—an atom is the smallest indivisible unit. Transactions are the same: they're the smallest unit of database operations; there's no such thing as "half-executed."

A more relatable example: you use an ATM to transfer money from account A to account B. This involves two steps: deduct 100 from A, add 100 to B. If A is deducted but B isn't added (e.g., system crash), the 100 disappears into thin air. Transactions ensure both steps either complete or neither happens—if something goes wrong mid-process, A's deduction is automatically reversed.

The four properties of transactions (ACID):

| Property | Meaning | Xiaohong's Example |
|----------|---------|-------------------|
| **A**tomicity | All succeed or all fail | The three order steps either all complete or all rollback |
| **C**onsistency | Data remains valid before and after | Inventory never goes negative |
| **I**solation | Concurrent operations don't interfere | Two people grabbing the last item, only one succeeds |
| **D**urability | Committed data is permanently saved | Server restarts, order data remains |

<CRUDVisualizer mode="tx" />

You don't need to implement transaction logic yourself—AI will wrap it using ORM's transaction API. You just need to review: which operations should be in the same transaction.

The criteria is simple: **if several operations must succeed or fail together, put them in one transaction.**

Common scenarios needing transactions:

- Placing an order (create order + deduct inventory)
- Transferring money (deduct from A + add to B)
- Registration (create user + create default config + send welcome message)
- Posting (create post + update user post count + create activity record)

::: warning Keep Transactions Short and Sweet
During transaction execution, database locks are held, blocking other operations. If a transaction includes calling external APIs, sending emails, or other time-consuming operations, locks are held for extended periods, causing other user requests to queue and wait.

Imagine you're at a bank counter: after transferring money, the teller also makes a confirmation call, sends a text notification, and prints a receipt for you… everyone in line behind you waits. The correct approach: the teller only does the transfer (database operation), you leave the counter, and make calls and send texts yourself (outside the transaction).

Principle: **only put database operations in transactions**, keep external calls outside. For example, "create order + send notification" should create the order in a transaction, then send the notification after the transaction commits.
:::

---

## Schema Definition: The Blueprint for Your Database

Before having AI write CRUD, you need to define your table structure (Schema). Schema is the "blueprint" for your database, describing what tables exist, what columns each table has, and the types and constraints of those columns.

Think of Schema as architectural drawings. Before building a house, the architect draws plans: how many floors, rooms per floor, room sizes, where doors and windows go. Databases work the same—first draw the "blueprint" (Schema), then "move people in" (store data).

You don't write Schema by hand—AI generates it automatically based on your business requirements. But you need to be able to read it and know if it's designed correctly.

After AI generates Schema, review these points:

- **Is the primary key set correctly?** Every table should have an `id` primary key
- **Are foreign key directions correct?** `posts.userId` should point to `users.id`, not reversed. Foreign keys always go on the "many" side—one user has many posts, so `userId` is in the `posts` table, not the `users` table
- **Are necessary constraints added?** The likes table's `userId + postId` should have a composite unique constraint—"the combination of these two fields cannot repeat," meaning the same user can only like the same post once. Without this constraint, double-clicking creates two like records
- **Are field types appropriate?** Post content should use `text` (unlimited length), not `varchar(255)`—as covered in 6.2, in PostgreSQL both perform the same, `varchar` just adds an unnecessary length limit
- **Are timestamps complete?** `created_at` and `updated_at` are standard and should be on almost every table
- **Are indexes considered?** Fields frequently used for queries (like `userId`, `postId`) should have indexes—covered in detail in 6.4

---

## Database Migrations: Version Control for Schema Changes

Table structures aren't set in stone. As features iterate, you might need to add an `avatar` field to the `users` table, or an `is_pinned` column to the `posts` table.

Change the database directly? Too risky. If you make a mistake, production data is destroyed. And how do you remember what you changed? Looking back three months later, you have no idea why that column was added.

**Migrations** are "version control" for databases, like Git manages code versions. Every table structure change generates a migration file recording "what was changed." This enables:

- **Tracking history**: knowing how the table structure evolved step by step, with every change recorded
- **Team collaboration**: teammates pull code, run migrations, and database structure syncs automatically—no manual alignment needed
- **Safe rollback**: if something goes wrong, revert to the previous version, like Git's `revert`

Drizzle ORM has built-in migration tools. The workflow is: you modify Schema code (e.g., add an `avatar` column to `users` table), run the migration command, the tool automatically compares old and new Schema differences, generates a migration SQL file (e.g., `0001_add_avatar_to_users.sql`), and finally executes this file to update the database.

::: warning Be Careful with Production Migrations
Development environments can be changed freely, but production migrations require caution. Some operations are **irreversible**: deleting a column permanently loses its data; changing `text` to `integer` loses data that doesn't match the format.

After AI generates a migration, always test in development first, confirm it's correct before applying to production. Especially for operations involving column deletion or type changes, be extra cautious. When in doubt, backup first.
:::

---

## Workflow Summary

As a Vibe Coder, your workflow for working with databases looks like this:

1. **Design phase**: Describe your business requirements clearly, AI generates Schema
2. **Review Schema**: Check table structure, relationships, and constraints (using knowledge from 6.2)
3. **Generate CRUD**: AI generates CRUD code based on Schema
4. **Review CRUD**: Focus on WHERE clauses, transaction usage, and error handling (using knowledge from this section)
5. **Iterate**: Requirements change, AI modifies Schema and generates migrations
6. **Test and verify**: Run through in development, then deploy to production

Notice your role in this workflow: **you're not "the person who writes database code," you're "the person who reviews database code."** AI writes, you judge if it's correct.

This is why the previous two sections spent so much time on concepts—only by understanding primary keys, foreign keys, constraints, and relationship types can you effectively review AI's output. Without understanding these concepts, you're just "whatever AI says goes," and when problems arise, you won't know where to start troubleshooting.

---

::: tip Key Takeaways from This Section

- **ORM** is the translation layer between code and database; Drizzle is recommended
- **CRUD** is the foundation of all database operations: Create, Read, Update, Delete
- **Transactions** ensure multiple operations either all succeed or all fail
- **Review focus**: WHERE clauses, foreign key directions, unique constraints, transaction boundaries
- **Migrations** are version control for table structures; schema changes must go through the migration process
:::