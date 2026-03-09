---
title: "4.6 Configuration File Formats"
description: "Understand JSON and YAML configuration formats"
chapter: "Chapter 4"
priority: "🟢"
---

# 4.6 Configuration File Formats 🟢

> **After reading this section, you will gain:**
>
> - An understanding of the syntax and uses of JSON and YAML
> - Mastery of how to read and write both formats
> - Familiarity with their application scenarios in development
> - An understanding of the value of structured data for AI

> JSON and YAML are the "common language" of the digital age—the universal languages different systems use to communicate.

---

## What Are Structured Data Formats

Structured data formats are ways of representing data using strict rules for organization. They allow computers to parse and understand data accurately.

Compared with natural language, structured formats are:

- Consistent in format, with no ambiguity
- Easy for programs to parse and generate
- Universal across languages and platforms
- Easy for AI to understand accurately

To understand the value of structured data, it helps to look at the evolution of programming languages. Early programming languages were closer to the machine, and programmers had to work directly with memory addresses and binary data. As high-level languages emerged, data was abstracted into concepts like variables, objects, and arrays, allowing programmers to describe data in more natural ways. JSON and YAML represent the highest level of this abstraction—they are not syntax for any one specific language, but rather a "common language" that all modern languages can understand. Whether you use Python dictionaries, JavaScript objects, or Go structs, they can all ultimately be converted to JSON without loss and parsed by other languages. This universality is the foundation that makes interoperability possible in the modern software ecosystem.

::: tip How AI Prefers to Read

Compared with loose natural language, clearly structured formats are the kind of "documentation" AI likes best. When you write requirements or configuration in JSON/YAML, AI can understand them much more accurately.

:::

---

## JSON Format

**JSON (JavaScript Object Notation)** is the most commonly used data exchange format.

### Syntax Rules

```json
{
  "name": "John",
  "age": 25,
  "email": "john@example.com",
  "address": {
    "city": "Beijing",
    "district": "Chaoyang"
  },
  "hobbies": ["reading", "swimming", "programming"]
}
```

**Rule overview**:

- Use curly braces `{}` to represent objects
- Use square brackets `[]` to represent arrays
- Data is organized as "key: value" pairs
- Keys must be wrapped in double quotes
- Key-value pairs are separated by commas

### Data Types

| Type | Example | Description |
|------|------|------|
| String | `"hello"` | Wrapped in double quotes |
| Number | `123`, `3.14` | Integer or floating-point number |
| Boolean | `true`, `false` | True/false |
| Array | `[1, 2, 3]` | An ordered list of data |
| Object | `{"key": "value"}` | A collection of key-value pairs |
| null | `null` | Empty value |

### Advantages of JSON

| Advantage | Description |
|------|------|
| **Universality** | Supported by all programming languages |
| **Readability** | Easy for humans to read |
| **Compactness** | Concise format that doesn’t waste space |
| **Web Standard** | The standard format for HTTP APIs |

::: tip JSON Is the Common Language of the Digital Age

Whether you're writing a backend in Python, a frontend in JavaScript, or having AI write code, everyone uses JSON to pass data around. Without JSON, each language might have its own "dialect" format, like how different regions once had their own languages, making communication much harder.

:::

---

## YAML Format

**YAML (YAML Ain't Markup Language)** is a more human-friendly configuration format.

### Syntax Rules

```yaml
# User info
name: John
age: 25
email: john@example.com

# Address info
address:
  city: Beijing
  district: Chaoyang

# Hobbies
hobbies:
  - reading
  - swimming
  - programming
```

**Rule overview**:

- Use indentation to represent hierarchy (spaces only, no tabs)
- Separate keys and values with a colon
- Use a hyphen `-` for array items
- `#` at the beginning indicates a comment

### Advantages of YAML

| Advantage | Description |
|------|------|
| **More readable** | Feels as natural as writing a checklist |
| **Supports comments** | You can add explanatory text |
| **Concise** | No need for braces, quotes, or commas |
| **Well-suited for configuration** | Commonly used for config files |

::: tip YAML vs JSON

YAML is like a "checklist," while JSON is like a "table." YAML feels easier for writing configuration files, while JSON is more standard for data transfer.

:::

---

## CSV: A Flat File Format

When discussing structured data, **CSV (Comma-Separated Values)** is one of the simplest formats. It stores tabular data as plain text, with each line representing a record and fields separated by commas.

**CSV example**:

```csv
name,email,age
John,john@example.com,25
Jane,jane@example.com,30
```

**Characteristics of CSV**:

- **Simple**: Can be opened with any text editor
- **Highly compatible**: Can be imported directly into Excel and Google Sheets
- **Small in size**: No extra formatting markup

**Limitations of CSV**:

- Can only represent two-dimensional tables (rows and columns)
- Does not support nested structures
- Has no data types (everything is a string)
- Struggles with complex relationships

**CSV vs JSON/YAML**:

| Feature | CSV | JSON/YAML |
|------|-----|-----------|
| Structure | Two-dimensional table | Arbitrary nesting |
| Data types | None | Strings, numbers, booleans, etc. |
| Relationship modeling | Weak | Strong |
| Use cases | Simple data export, table exchange | Config files, API data, complex structures |

CSV is a great fit when you need to export data from a spreadsheet or exchange simple data with coworkers who use Excel. But for Web application development, JSON and YAML are better choices because they can represent more complex data structures.

---

## Comparing the Two Formats

<ConfigFormatPlayground />

| Feature | JSON | YAML |
|------|------|------|
| Syntax | Strict, requires braces and quotes | Flexible, based on indentation |
| Comments | Not supported | Supports `#` |
| Readability | Good | Better |
| Use cases | Data transfer, APIs | Config files |
| Parsing speed | Faster | Slightly slower |

---

## Real-World Examples

### JSON: User Data

```json
{
  "id": "user_123",
  "name": "John",
  "email": "john@example.com",
  "avatar": "https://example.com/avatar.jpg",
  "location": {
    "country": "China",
    "province": "Beijing",
    "city": "Beijing"
  },
  "birthday": "1990-01-15",
  "phone": "+86 138 0000 0000"
}
```

### YAML: App Configuration

```yaml
# App configuration
app:
  name: "My Blog"
  version: "1.0.0"
  port: 3000

# Database configuration
database:
  host: "localhost"
  port: 5432
  name: "blog_db"
  user: "admin"
  password: "${DB_PASSWORD}"  # Reference environment variable

# Feature flags
features:
  enable_comments: true
  enable_analytics: false
```

### JSON: API Response

```json
{
  "success": true,
  "data": {
    "posts": [
      {
        "id": "1",
        "title": "First Article",
        "author": "John"
      },
      {
        "id": "2",
        "title": "Second Article",
        "author": "Jane"
      }
    ],
    "total": 2,
    "page": 1
  }
}
```

### YAML: CI/CD Configuration

```yaml
# GitHub Actions configuration
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: pnpm install
      - name: Build
        run: pnpm build
      - name: Deploy
        run: pnpm deploy
```

---

## Usage in Development

### package.json (JSON)

The configuration file for Node.js projects, which defines project dependencies and scripts:

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.0.0"
  }
}
```

### tsconfig.json (JSON)

TypeScript configuration file:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "jsx": "preserve",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

---

## Frequently Asked Questions

### Q1: What about comments in JSON?

The JSON standard does not support comments. If you need comments, you can use JSONC (JSON with Comments) or switch to YAML.

### Q2: Should YAML indentation use spaces or tabs?

Spaces only, never tabs. Typically, 2 spaces are used for one indentation level.

### Q3: How do I choose between JSON and YAML?

- Data transfer, API responses: use JSON
- Config files: prefer YAML
- Need comments: use YAML
- Web APIs: use JSON

### Q4: What if I get the format wrong?

Most editors provide syntax checking. Asking AI to help fix it is also a good option—it can point out the exact error and give you the correct format.

---

## Key Takeaways

- ✅ JSON and YAML are standard formats for structured data
- ✅ JSON is the universal data format for Web APIs
- ✅ YAML is better suited for writing config files
- ✅ Structured formats help AI understand requirements more accurately
- ✅ JSON uses braces and quotes, while YAML uses indentation
- ✅ The right format depends on the use case

Now that you understand configuration formats, the next step is learning how to apply this knowledge in real scenarios—by integrating external APIs.

---

## Related Content

- Prerequisite: [4.2 The Relationship Between PRDs and Technical Documentation](./02-prd-and-tech-docs.md)
- Prerequisite: [4.5 Frontend-Backend Separation Concepts](./05-frontend-backend-separation.md)
- See also: [4.7 API Integration in Practice](./07-api-integration.md)
- See also: [4.8 README Structure](./08-readme-structure.md)