---
title: "6.2 Database Fundamentals"
description: "Comprehensive guide to relational database core concepts: tables, rows, columns, primary keys, foreign keys, relationship types, constraints, and data types"
---

# 6.2 Database Fundamentals

> **Learning Objectives**: Master the core concepts of relational databases—tables, rows, columns, primary keys, foreign keys, relationship types, and constraint mechanisms.

---

## Xiaohong's Food Delivery Startup

Xiaohong is a doer. She noticed that campus food delivery platforms charge excessive fees and offer limited restaurant options, so she decided to build her own campus delivery platform—serving only her university, using student couriers, and taking half the commission of Meituan.

She first mapped out the data she needed to manage:

- **Student users**: Who's using the platform? Phone number, nickname, dorm address
- **Campus merchants**: Cafeteria stalls, bubble tea shops, fruit stores—each with name, rating, business hours
- **Menu items**: What each restaurant sells, how much it costs, whether there are photos
- **Order records**: Who bought what from which store, how much they paid, current status
- **Order details**: Which specific dishes were in each order, how many of each

Xiaohong initially considered using Excel—after all, she'd used it for class schedules and budgeting in college, and it felt familiar.

But she quickly realized: one order might contain three dishes from different categories, and the same dish might appear in hundreds of orders. These relationships formed a web that Excel's "one row per record" structure couldn't handle. She tried cramming multiple dish names into a single cell separated by commas, but querying and analyzing became a nightmare.

What she needed was a tool that could manage "relationships between data"—this is the **relational database**.

The word "relational" is key. It doesn't mean the database helps you manage interpersonal relationships; it means it excels at managing **relationships between data**: user-to-order relationships, order-to-dish relationships, merchant-to-dish relationships. These relationships are hard to express in Excel but are natively supported in relational databases.

---

## Tables: "Worksheets" 100x More Powerful Than Excel

A **table** in a database can initially be understood as a "super Excel worksheet." If you've used Excel, you already have a great starting point for understanding database tables.

Here's what's similar:

| Excel Concept | Database Concept | Description |
|-------------|----------------|-------------|
| Worksheet Sheet | Table | One table per data type, e.g., `users` table stores all users |
| Row of data | Row (also called record) | A specific user, a specific order |
| Header column names | Column (also called field) | Data attributes, e.g., `phone`, `address` |

Now here's where they differ—these differences are where databases truly shine, and what Excel can never do:

**Strict data types.** In Excel, a column can mix text and numbers—no one stops you from typing "very old" in an "age" column. Databases won't allow this—a column with `integer` type only accepts integers; try to insert text and the database rejects it outright. This seems "inconvenient," but it's precisely what ensures data quality. Imagine Xiaohong's delivery platform: if the "price" column gets contaminated with text, checkout will fail. Database strict typing prevents this at the source.

**Constraint mechanisms.** Databases can enforce rules: phone numbers can't be duplicated (UNIQUE), nicknames can't be empty (NOT NULL), order user IDs must point to real existing users (FOREIGN KEY). Data that violates these rules can't be written—dirty data is blocked at the source. Once these rules are set, anyone writing data through any method must follow them—unlike Excel, where anyone can arbitrarily modify any cell.

**Cross-table relationships.** This is the database's core capability, and why it's called "relational." Multiple tables can establish relationships through "foreign keys," then use SQL JOIN operations to retrieve related data in one query. For example, "find all of Xiaohong's orders and the dishes in each order"—one SQL statement handles it. In Excel, you'd be jumping between multiple sheets, manually matching IDs, extremely inefficient.

**Concurrency safety.** When 100 users place orders simultaneously, databases use transactions and locking mechanisms to ensure each order is written correctly—no overwrites, no lost data. Excel can't do this—two people editing the same file simultaneously leads to conflicts at best, lost changes at worst.

The interactive component below lets you intuitively experience a table's structure—click column names to view detailed explanations of each field:

<DatabaseVisualizer />

---

## Primary Keys: The ID Card for Every Row

In the real world, everyone has a national ID number—unique nationwide—used across all systems to identify "who you are." Opening a bank account requires it, hospital registration requires it, buying train tickets requires it—all systems use this number to confirm "you are you."

Databases work the same way. Every table needs a **primary key** to uniquely identify each row of data. Just as an ID number identifies a person, a primary key identifies a record.

Primary key rules are simple—just three:

- **Unique**: No two rows can have the same primary key value (just as no two people can share an ID number)
- **Not null**: Every row must have a primary key value (everyone must have an ID number)
- **Immutable**: Once assigned, it shouldn't change (you don't swap ID numbers every few days)

In practice, primary keys typically use an `id` column. Modern PostgreSQL recommends `bigint generated always as identity`—meaning "use a large integer as ID, automatically generated by the database, incrementing by 1 each time" (more standard and safer than the older `serial`):

```sql
CREATE TABLE users (
  id    bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  phone text   NOT NULL
);
```

> In Drizzle ORM, writing `serial('id').primaryKey()` works perfectly fine too—AI-generated code commonly uses both. The core principle: let the database auto-assign IDs so you don't have to worry about it.

You might ask: phone numbers are also unique, why not use them as the primary key?

The reason is that phone numbers have "business meaning"—users might change numbers. Once a primary key is referenced by other tables (e.g., the orders table stores `user_id`), changing the primary key means updating everywhere it's referenced—a ripple effect. So industry best practice is: **use a meaningless auto-incrementing ID as the primary key**—simple, stable, efficient.

---

## Foreign Keys: The Thread Between Tables

If the primary key is "who I am," then the foreign key is "who I'm related to."

A **foreign key** is a column in one table that points to the primary key of another table. Its purpose is to establish relationships between tables while ensuring the referenced data actually exists.

Example: Xiaohong's `orders` table has a `user_id` column whose value must be a real existing `id` from the `users` table. This is a foreign key. Through this `user_id`, you can trace from an order back to the user who placed it—"Who placed this order? `user_id` is 42, look up id=42 in the `users` table—oh, it's Xiaohong."

```sql
CREATE TABLE orders (
  id          serial PRIMARY KEY,
  user_id     integer REFERENCES users(id),  -- foreign key pointing to users.id
  amount      real    NOT NULL,
  status      text    DEFAULT 'pending_payment'
);
```

Foreign keys provide three benefits:

**Data consistency guarantee.** You can't create an order for a non-existent user. If there's no user with id=999 in the `users` table, inserting an order with `user_id=999` into `orders` will fail immediately. This is "referential integrity"—what you reference must actually exist; you can't point to a non-existent user.

**Relationship establishment.** Through `user_id`, you can trace from an order to its user, or from a user to all their orders. Relationships between data become clear and queryable.

**Cascade operations (chain reactions).** When deleting a user, what happens to their orders? The database can automatically delete all their orders (cascade delete), or prevent deletion (don't allow if related orders exist). This behavior can be configured when creating the table.

::: warning Foreign key columns must be indexed
PostgreSQL does **not** automatically create indexes on foreign key columns. If the `orders` table has 100,000 rows, querying "all orders for a specific user" without an index on `user_id` causes a full table scan—flipping from first row to last like searching a book without a table of contents—100x slower or more. So every foreign key column needs a manual index. This topic is covered in detail in section 6.4.
:::

> You can click on connections in the "Relationship Diagram" tab of the component above to view specific foreign key association methods.

---

## Relationship Types Explained

Relationships between database tables boil down to just three types. Understanding these three, and you can read any database design.

### One-to-Many (1:N) — Most Common

One user can place many orders, but each order belongs to only one user. This is one-to-many.

```
users (1) ──→ orders (N)
  id              user_id → users.id
```

The implementation is intuitive: add a foreign key column to the "many" side. The `user_id` in the `orders` table pointing to `users.id` establishes the one-to-many relationship.

One-to-many is the most common relationship type—you'll find countless examples in any application:

- One merchant has many dishes (merchant → dish)
- One author writes many articles (author → article)
- One class has many students (class → student)
- One user posts many updates (user → update)

The test is simple: ask yourself "Can one A have many Bs? Does one B belong to only one A?" If both are "yes," it's one-to-many.

### Many-to-Many (M:N) — Requires Junction Table

One order contains many dishes, and one dish appears in many orders. This "multiplicity on both sides" is a many-to-many relationship.

Many-to-many can't be represented with a single foreign key—because a foreign key can only point to one record, not multiple simultaneously. The solution is to add a **junction table**, splitting many-to-many into two one-to-many relationships:

```
orders (M) ←─ order_items ─→ dishes (N)
  id            order_id        id
                dish_id
                quantity
```

`order_items` is the junction table. Each row records "which order bought which dish, how many." One order has multiple rows in `order_items` (ordered multiple dishes), and one dish also has multiple rows in `order_items` (ordered by multiple orders).

The junction table isn't just a "connector"—it can also store attributes of the relationship itself. For example, `quantity` (amount) is an attribute of "the relationship between order and dish"—it belongs neither to the order nor the dish, only to this specific association.

More many-to-many examples:

- Student ↔ Course (junction table: enrollment records, can store grades)
- User ↔ Tag (junction table: user tags, can store tagging time)
- Article ↔ Category (junction table: article-category association)

### One-to-One (1:1) — Splitting Large Tables

One user corresponds to one detailed profile. Why not put them in the same table?

Imagine the `users` table has 500,000 users. Every time a user logs in, the system queries `users` to verify identity. If this table is stuffed with rarely used fields like avatar URL, bio, preference settings, address list, etc., the table becomes "heavy" and query speed degrades.

The solution is to split rarely used fields into another table:

```sql
CREATE TABLE profiles (
  id       serial PRIMARY KEY,
  user_id  integer UNIQUE REFERENCES users(id),  -- UNIQUE enforces one-to-one
  avatar   text,
  bio      text
);
```

The key is the **UNIQUE** constraint on `user_id`. Without it, one user could have multiple profiles, becoming one-to-many. With UNIQUE, the database guarantees each `user_id` appears only once—strict one-to-one.

One-to-one is less common in practice, but useful in these scenarios:

- User basic info ↔ User detailed profile (splitting large tables)
- Order ↔ Invoice (not every order has an invoice)
- Employee ↔ Workstation (one employee, one workstation)

---

## Data Integrity: Why Constraints Matter

A database without constraints is like a warehouse without rules—anything can be dumped in, and chaos is inevitable. Today you insert a user with "age -5," tomorrow a product with "null price," and the day after your app crashes in strange places with no clue it's a data problem.

**Constraints** are rules automatically enforced by the database. Every time data is written, the database checks these rules—non-compliant data is rejected outright, not even stored. This is like airport security—no matter who you are or where you're from, luggage goes through X-ray, and prohibited items are intercepted.

| Constraint | Purpose | Real-world Analogy |
|-----------|---------|-------------------|
| PRIMARY KEY | Uniquely identifies each row | National ID, unique nationwide |
| FOREIGN KEY | Ensures referenced data exists | Recipient on package label must be real person |
| NOT NULL | Required field | Required form fields, can't submit empty |
| UNIQUE | No duplicates allowed | Phone number can't register twice |
| CHECK | Custom condition validation | Age must be > 0, rating must be 1-5 |
| DEFAULT | Auto-fills default when empty | Order status defaults to "pending payment" |

> In the "Constraint Demo" tab of the component above, you can see correct vs. violation comparisons for each constraint type.

You might think: why add constraints at the database level when I can validate in code?

Three reasons:

**Code can have bugs, but database constraints are the last line of defense.** Your validation logic might be wrong, miss a boundary condition, or have an `if` with a missing equals sign—all of which can let dirty data slip through. Database constraints don't make such mistakes; they're the impartial goalkeeper.

**Data has multiple entry points.** Your app has API endpoints, admin panels, data migration scripts, scheduled tasks... each can write data. You can't perfectly implement validation logic at every entry point. But database constraints only need to be defined once, protecting all entry points.

**Cleaning dirty data is extremely costly.** Imagine tens of thousands of orders with `user_id` pointing to non-existent users—how do you fix it? Delete these orders? What about the users' money? Change `user_id` to some default user? Then the data becomes distorted. Prevention is always cheaper than cure.

---

## Data Types Quick Reference

Every column must specify a data type. Choose right for efficient storage and fast queries; choose wrong and you waste space or lose data.

Common PostgreSQL data types:

| Type | Purpose | Example | Notes |
|------|---------|---------|-------|
| `bigint` / `serial` | Primary key ID | 1, 2, 3... | `bigint` is safer, `serial` is sufficient |
| `text` | Variable-length text | 'Hello World' | Always use `text`, never `varchar(n)` |
| `integer` | Integer | 42, -1, 0 | Max 2.1 billion, use when sufficient |
| `boolean` | True/false | true / false | — |
| `timestamptz` | Date and time | '2024-01-15 09:30:00+08' | With timezone, **never use** `timestamp` |
| `jsonb` | Structured JSON | '{"tags":["vip"]}' | Can build GIN index for fast queries |
| `numeric(10,2)` | Exact decimal | 99.99 | Must use for money, never `real` |
| `uuid` | Globally unique identifier | 'a1b2c3d4-...' | Common in distributed systems |

> In the "Data Types" tab of the component above, you can view the Drizzle ORM syntax for each type.

::: warning Three common type pitfalls

1. **Using `real` / `float` for money**: Floating-point has precision issues (0.1 + 0.2 ≠ 0.3). Money must use `numeric` or `integer` (unit: cents)
2. **Using `timestamp` for time**: Timestamps without timezone cause ambiguity across servers. Always use `timestamptz`
3. **Using `varchar(255)` for strings**: In PostgreSQL, `text` and `varchar` have identical performance; `varchar(n)` just adds a length limit with zero performance benefit
:::

---

## SQLite vs PostgreSQL

| | SQLite | PostgreSQL |
|--|--------|------------|
| Position | File-based database, zero configuration | Full database server |
| Storage | Single `.db` file | Standalone database service process |
| Concurrency | Single-writer multiple-reader, small scale | High-concurrency read/write, production-ready |
| Data types | Loose (only 5 actual types) | Strict and rich (50+ types) |
| JSON support | Basic | Powerful `jsonb` type |
| Use cases | Local development, embedded, prototyping | Production, team collaboration, high concurrency |
| Tutorial recommendation | Good for learning, quick start | Recommended for real projects, Supabase offers free tier |

::: tip Selection advice
If you use cloud database services like Supabase or Neon, you get PostgreSQL directly—no decision needed. For local development and quick validation, SQLite is perfectly adequate—Drizzle ORM supports seamless switching between both.
:::

---

## Let AI Help Design Your Schema

Once you understand these concepts, you can use prompts like this to have AI design your database:

```
I'm building [app description], please help me design the database schema.

Requirements:
1. Use PostgreSQL + Drizzle ORM
2. List all tables, fields, types, constraints
3. Annotate relationships between tables (one-to-many/many-to-many)
4. Every table must have id, created_at, updated_at
5. Use Chinese comments explaining each field's purpose
```

AI will generate complete Drizzle schema code that you can copy directly into your project.

---

::: tip Key Takeaways from This Section

- **Table** = Container for one type of data; rows are records, columns are attributes
- **Primary key** = Unique ID for each row, use `serial` auto-increment
- **Foreign key** = Thread connecting tables, ensures referential integrity
- **Three relationship types**: One-to-many (most common), many-to-many (needs junction table), one-to-one (splits large tables)
- **Constraints** = Database-level rule guards, final defense beyond code validation
- **Data types** = Choose right for efficient storage and fast queries
:::