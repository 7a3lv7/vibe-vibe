---
title: "Testing If Bash Is All You Need: SQL vs Bash Agent Comparison"
description: Vercel and Braintrust jointly tested Bash vs SQL agent tools on structured data queries—hybrid approaches with self-verification achieved 100% accuracy
author: Ankur Goyal, Andrew Qu
source: https://vercel.com/blog/testing-if-bash-is-all-you-need
date: '2026-03-07'
category: 02-technical-architecture
tags:
  - Vercel
  - Braintrust
  - Agent Engineering
  - Tool Selection
  - Performance Benchmarking
---

# Testing If Bash Is All You Need: SQL vs Bash Agent Comparison

**Author: Ankur Goyal, Andrew Qu (Vercel & Braintrust)**

**Original: [Read the full article](https://vercel.com/blog/testing-if-bash-is-all-you-need)**

<div class="article-meta">
</div>

> ## Summary
>
> When Vercel claimed agents could work effectively with just filesystem and bash, the natural question was: how does that compare to purpose-built tools like SQL? This empirical study provides the answer—and the most important finding isn't about which tool wins.

## The Question

Following Vercel's exploration of filesystem-and-bash agents, a natural follow-up question emerged: if bash works so well for general data exploration, how does it compare to SQL—a language specifically designed for structured data queries?

To find out, Vercel partnered with Braintrust to run a rigorous empirical comparison. The experiment tested different agent tool configurations on a standardized set of data analysis tasks, measuring accuracy, cost, and reliability.

## Experiment Setup

### The Benchmark

The team designed a set of data analysis tasks that require querying structured data—the kind of work where SQL is traditionally the obvious choice. Tasks ranged from simple lookups ("What was the total revenue in Q3?") to complex aggregations ("Which product category had the highest month-over-month growth rate in the last 6 months?").

### Agent Configurations

Three agent configurations were tested:

1. **Pure SQL Agent**: Given access only to a SQL database and a tool to execute SQL queries
2. **Pure Bash Agent**: Given access only to the data as CSV/JSON files and a bash terminal
3. **Hybrid SQL+Bash Agent**: Given access to both SQL and bash, plus a self-verification step

Each agent received the same underlying language model and the same data. The only variable was the tool set and interaction pattern.

## Results

### Pure SQL Agent: 100% Accuracy

The SQL agent performed flawlessly on structured data tasks. This isn't surprising—SQL is purpose-built for exactly this type of work. The agent could:

- Write precise queries to extract exactly the needed data
- Use JOINs, GROUP BY, and window functions naturally
- Handle complex aggregations in a single query
- Leverage database indexing for efficient execution

SQL's declarative nature aligns perfectly with data analysis: you describe *what* you want, not *how* to get it. The model's extensive training on SQL means it writes accurate queries consistently.

### Pure Bash Agent: Good but Not Perfect

The bash agent achieved high accuracy but fell short of SQL for structured data tasks. While it could handle many queries correctly using tools like `awk`, `sort`, `uniq`, and `jq`, certain operations were cumbersome:

- Complex multi-table joins required multi-step pipelines prone to errors
- Aggregations with multiple grouping levels were harder to express
- Edge cases in CSV parsing (quoted fields, escaped commas) occasionally tripped up the shell-based approach
- Some calculations required writing small scripts, adding complexity

The bash agent excelled at tasks involving text search, pattern matching, and exploring data structure—areas where the filesystem approach shines. But for pure structured data analysis, SQL's purpose-built abstractions gave it a clear edge.

### Hybrid SQL+Bash with Self-Verification: 100% Accuracy

The most interesting result came from the hybrid configuration. Given access to both SQL and bash, plus an explicit self-verification step, this agent also achieved 100% accuracy.

The self-verification step was key: after generating an answer, the agent was instructed to verify its result using an alternative method. For example:

1. Run a SQL query to get the answer
2. Export the result
3. Use bash to independently verify the calculation
4. Compare results before reporting

Or conversely:

1. Use bash to explore the data and compute an answer
2. Write a SQL query to verify
3. Resolve any discrepancies

This cross-checking caught errors that either tool alone might miss.

## The Key Insight: Verification Matters More Than Tool Choice

The headline finding isn't "SQL beats bash" or "bash is good enough." It's this: **self-verification is more important than which tool you use**.

Both the pure SQL agent (100%) and the hybrid agent with verification (100%) achieved perfect accuracy. The hybrid agent achieved this despite using bash for some operations that were less suited to it—because the verification step caught and corrected any errors.

This has profound implications for agent design:

### Verification as a First-Class Concern

Most agent frameworks focus on tool selection and orchestration. This experiment shows that the verification step—having the agent check its own work—is at least as important as the tools themselves. A mediocre tool with good verification can match a perfect tool without verification.

### The Self-Verification Pattern

The self-verification pattern is straightforward to implement:

```
1. Agent performs the task using its preferred method
2. Agent independently verifies the result
   - Using a different tool or approach
   - Using a different query that should produce the same answer
   - Using sanity checks (totals should sum, counts should be positive, etc.)
3. If verification fails, agent corrects and re-verifies
4. Agent reports the verified result with confidence
```

This pattern applies beyond data analysis. Code agents can run tests after writing code. Documentation agents can verify accuracy against source code. Any agent can benefit from checking its own work before reporting.

## Practical Implications

### Choosing Tools for Your Agent

The results suggest a practical framework for tool selection:

| Data Type | Best Tool | Rationale |
|-----------|-----------|-----------|
| Structured data (tables, databases) | SQL | Purpose-built, declarative, precise |
| Unstructured text (logs, documents) | Bash | grep, awk, and pipes excel here |
| Mixed data | Hybrid | Use the right tool for each sub-task |
| Any critical task | + Verification | Always verify, regardless of tool |

### Don't Over-Engineer Tool Selection

A tempting takeaway might be: always give agents every possible tool. But there's a trade-off. More tools mean more decision complexity for the model. The experiment suggests that **a focused set of tools plus verification** outperforms **a broad set of tools without verification**.

If you're building an agent for structured data, give it SQL and add verification. If you're building one for document exploration, give it bash and add verification. Don't build both unless your use case genuinely spans both domains.

### Cost Considerations

The bash approach remains significantly cheaper for exploratory tasks:

- SQL requires maintaining a database server
- Bash works directly on files, eliminating infrastructure costs
- For read-heavy exploratory workloads, the filesystem approach uses fewer tokens
- But for complex analytical queries, SQL's single-query efficiency can actually use fewer tokens than multi-step bash pipelines

The optimal choice depends on your specific workload pattern.

## Beyond Data Analysis

While this experiment focused on data analysis, the self-verification principle generalizes broadly:

### Code Generation

An agent writing code can:
1. Write the code
2. Run the test suite
3. If tests fail, analyze failures and fix
4. Re-run tests to verify

This is already standard practice in coding agents—the experiment quantifies why it works.

### Content Generation

An agent writing documentation can:
1. Generate the documentation
2. Verify code examples actually compile and run
3. Check that API references match the actual API
4. Ensure cross-references are valid

### Decision Making

An agent making recommendations can:
1. Analyze data and form a recommendation
2. Check the recommendation against constraints
3. Verify calculations used in the analysis
4. Present the recommendation with verification evidence

## Key Takeaways

1. **Match tools to data types**: SQL for structured data, bash for unstructured exploration
2. **Always add self-verification**: It's the single most impactful agent design decision
3. **Verification can compensate for tool limitations**: A weaker tool + verification can match a stronger tool alone
4. **Keep the tool set focused**: More tools add complexity; better to master fewer tools with verification
5. **Cross-tool verification is strongest**: Verifying results with a different approach catches systematic errors that same-tool verification might miss

The experiment provides an empirical foundation for a principle many practitioners had intuited: in agent design, the ability to check your work matters as much as the ability to do the work.
