---
title: How to Build Agents with Filesystems and Bash
description: Vercel's approach to building AI agents using filesystems and bash as the primary interface—achieving 75% cost reduction while improving reliability
author: Ashka Stephen
source: https://vercel.com/blog/how-to-build-agents-with-filesystems-and-bash
date: '2026-03-07'
category: 02-technical-architecture
tags:
  - Vercel
  - Agent Architecture
  - Context Management
  - Cost Optimization
  - Filesystem
  - Bash
---

# How to Build Agents with Filesystems and Bash

**Author: Ashka Stephen (Vercel)**

**Original: [Read the full article](https://vercel.com/blog/how-to-build-agents-with-filesystems-and-bash)**

<div class="article-meta">
</div>

> ## Summary
>
> Instead of building complex retrieval pipelines and custom tool chains, Vercel discovered that organizing data into files and giving agents bash access achieves better results at 75% lower cost. LLMs are naturally proficient at filesystem navigation—use that to your advantage.

## The Core Finding: 75% Cost Reduction

The headline number is striking: by replacing custom retrieval pipelines with a simple filesystem-and-bash approach, Vercel reduced agent costs from approximately **$1 to $0.25 per API call**—a 75% reduction.

But cost is only part of the story. The filesystem approach also improved reliability, reduced development complexity, and made the system easier to debug and maintain. It's a case study in how the simplest architecture often wins.

## Why LLMs Are Natural at Filesystem Navigation

This approach works because of a fundamental insight about how large language models are trained:

### Training Data Alignment

LLMs have been trained on enormous amounts of code repository data and terminal interaction logs. They've seen millions of examples of:

- Navigating directory structures with `ls`, `cd`, and `find`
- Searching file contents with `grep`, `awk`, and `sed`
- Reading files with `cat`, `head`, and `tail`
- Piping output between commands
- Writing scripts to automate multi-step operations

This means filesystem navigation isn't something you need to teach the model—it's something the model already knows deeply. When you give an agent bash access, you're leveraging its strongest existing capabilities rather than asking it to learn new abstractions.

### Structural Alignment

Business data naturally maps to directory hierarchies. Consider a sales organization:

```
/data/
├── customers/
│   ├── acme-corp/
│   │   ├── profile.json
│   │   ├── contracts/
│   │   └── communications/
│   └── globex/
│       ├── profile.json
│       └── contracts/
├── products/
│   ├── enterprise-plan.md
│   └── starter-plan.md
└── reports/
    ├── q1-2024.csv
    └── q2-2024.csv
```

This structure is immediately navigable. The model can `ls /data/customers/` to see all customers, `cat /data/customers/acme-corp/profile.json` to read a specific profile, and `grep -r "renewal" /data/customers/` to find all renewal-related information. No custom query language, no API documentation, no special tools needed.

## The Technical Advantages

### Precise Retrieval vs. Semantic Similarity

Traditional RAG (Retrieval Augmented Generation) approaches rely on semantic similarity search—turning queries into vectors and finding the closest matches. This works, but it's fuzzy. You get "similar" results, not exact matches.

Filesystem search with tools like `grep` provides exact matches:

```bash
# Find all files mentioning a specific customer ID
grep -rl "CUST-12345" /data/

# Find all contracts expiring this quarter
grep -l "expires.*2024-Q3" /data/customers/*/contracts/

# Count total revenue by product
awk -F',' '{sum += $4} END {print sum}' /data/reports/q2-2024.csv
```

These operations are deterministic. You get exactly what you asked for, every time. No relevance scoring, no missed results, no hallucinated matches.

### Minimal Context Loading

One of the biggest challenges in agent design is managing the context window. Custom retrieval systems often load too much or too little information.

The filesystem approach solves this naturally:

- **Lazy loading**: The agent reads files only when it needs them
- **Selective reading**: `head -20 file.csv` reads just the header and first few rows
- **Targeted search**: `grep` finds exactly the lines that matter
- **Progressive exploration**: The agent can `ls` a directory first, then dive into specific files

This on-demand pattern means the context window stays focused on what's relevant, reducing token usage and improving response quality.

### Safety Through Sandboxed Isolation

Filesystem-based agents can be trivially sandboxed:

- Mount a read-only filesystem for data the agent should query but not modify
- Use container isolation to prevent access to anything outside the data directory
- Set resource limits (CPU, memory, execution time) on bash commands
- Log every command for auditability

This is dramatically simpler than building access control into custom tool APIs. The operating system's permission model does the heavy lifting.

## Eliminates Custom Retrieval Pipelines

Consider what a traditional approach requires:

1. **Data ingestion**: Parse documents, extract text, handle different formats
2. **Embedding generation**: Convert text chunks to vectors
3. **Vector storage**: Maintain a vector database with indexing
4. **Query processing**: Convert user queries to vectors, find nearest neighbors
5. **Re-ranking**: Score and filter results for relevance
6. **Context assembly**: Combine retrieved chunks into a coherent context

The filesystem approach replaces all of this with:

1. **Organize data into files and directories**
2. **Give the agent bash access**

That's it. No embedding models, no vector databases, no retrieval tuning. The reduction in system complexity is enormous.

## Real-World Example: Sales Call Agent

The Vercel team demonstrated this with a sales call analysis agent. The task: help sales teams analyze customer interactions and extract insights.

### Data Organization

Sales data was organized into a natural directory structure:

```
/sales-data/
├── calls/
│   ├── 2024-01-15-acme/
│   │   ├── transcript.txt
│   │   ├── summary.json
│   │   └── action-items.md
│   └── 2024-01-16-globex/
│       ├── transcript.txt
│       ├── summary.json
│       └── action-items.md
├── playbooks/
│   ├── discovery-call.md
│   ├── demo-script.md
│   └── objection-handling.md
└── metrics/
    ├── conversion-rates.csv
    └── pipeline-status.json
```

### Agent Interaction

When asked "What objections came up in recent calls with enterprise prospects?", the agent:

1. Lists call directories to identify recent calls
2. Reads summary files to identify enterprise prospects
3. Searches transcripts for objection-related patterns
4. Cross-references with the objection-handling playbook
5. Synthesizes findings into an actionable report

Every step uses standard bash commands. No custom tools. No retrieval pipeline. The model navigates the data the same way a human would navigate their file system.

## The Pattern: Organize → Grant Access → Navigate

The generalizable pattern is straightforward:

### 1. Organize Your Data

Structure your domain data into a logical directory hierarchy. Think about how a human would want to browse and search this information:

- Use meaningful directory and file names
- Store related information together
- Use standard formats (JSON, CSV, Markdown) that are easy to read and parse
- Include metadata in file names or metadata files

### 2. Grant Bash Access

Give the agent access to a sandboxed bash environment with the organized data mounted:

- Read-only access for query-only use cases
- Appropriate write permissions if the agent needs to create reports or update records
- Resource limits to prevent runaway commands

### 3. Let the Model Navigate

The model uses familiar terminal idioms to explore and analyze the data:

- `ls` and `find` for discovery
- `cat`, `head`, `tail` for reading
- `grep`, `awk`, `sort` for searching and filtering
- `wc`, `uniq`, `jq` for aggregation and transformation
- Shell scripts for complex multi-step operations

## When to Use This Approach

The filesystem-and-bash pattern works best when:

- **Data is document-oriented**: Files, reports, logs, transcripts
- **Queries are exploratory**: The agent doesn't know exactly what it needs upfront
- **Cost matters**: You want to minimize token usage and API costs
- **Simplicity is valued**: You want to avoid maintaining complex infrastructure
- **Debugging matters**: You need to understand exactly what the agent did

It's less suitable when:

- Data requires complex joins across many relationships (use SQL instead)
- Real-time data with high write throughput is involved
- You need transactional guarantees on data modifications
- The data volume is too large to practically organize into files

## Key Takeaway

The filesystem-and-bash approach isn't just a clever hack—it's a recognition that LLMs already have a powerful, well-trained interface for data exploration. Instead of building new abstractions on top of that capability, use it directly. Organize your data in a way that aligns with what the model already knows how to do, and let it work the way it was trained to work.

The 75% cost reduction is impressive, but the real win is architectural simplicity. Fewer moving parts means fewer failure modes, easier debugging, and faster iteration.
