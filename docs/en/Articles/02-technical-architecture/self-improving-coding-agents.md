---
title: Self-Improving Coding Agents
description: How to design autonomous coding loops that work while you sleep—task decomposition, external memory, and automated validation for continuous AI development
author: Addy Osmani
source: https://addyosmani.com/blog/self-improving-agents/
date: '2026-03-07'
category: 02-technical-architecture
tags:
  - Coding Agents
  - Autonomous Coding
  - Continuous Integration
  - Agent Loops
  - Claude Code
---

# Self-Improving Coding Agents

**Author: Addy Osmani**

**Original: [Read the full article](https://addyosmani.com/blog/self-improving-agents/)**

<div class="article-meta">
</div>

> ## Summary
>
> The most productive use of AI coding agents isn't watching them work—it's setting them up to work autonomously while you do something else. This article explores the patterns, strategies, and safeguards for building continuous coding loops that improve themselves over time.

## The Vision: Agents That Work While You Sleep

The ultimate promise of AI coding agents isn't faster pair programming—it's autonomous productivity. Imagine describing a feature, going to bed, and waking up to a completed, tested, and reviewed pull request. We're not fully there yet, but the patterns for getting close already exist.

Self-improving coding agents work in continuous loops: they write code, test it, analyze failures, fix issues, and iterate—without human intervention. The key is setting up the right structure so these loops converge on correct solutions rather than spinning endlessly.

## The "Ralph Wiggum" Technique

One of the most effective strategies for autonomous coding agents has an unlikely name. The "Ralph Wiggum" technique (a reference to the Simpsons character who succeeds through persistent, incremental effort) is about breaking complex tasks into tiny, independently verifiable steps.

### Why Tiny Steps Work

Large tasks have compounding error rates. If each step in a 10-step task has a 90% success rate, the overall success rate is only 35%. But if you break that same task into 20 smaller steps, each with a 98% success rate, overall success climbs to 67%. And critically, failures are caught early and corrected before they cascade.

### The Pattern in Practice

Instead of asking an agent to "implement user authentication," break it down:

1. Create the user model with email and hashed password fields
2. Write a test for user creation
3. Implement the user creation function
4. Run tests and verify they pass
5. Create the login endpoint
6. Write a test for successful login
7. Write a test for failed login
8. Implement the login logic
9. Run all tests
10. Add session management
11. ...and so on

Each step is small enough that the agent can complete it reliably, and each step is independently verifiable. If step 6 fails, you know exactly where the problem is, and the agent can focus its attention on a small, well-defined fix.

### Verification at Every Step

The critical element is that every step includes its own verification:

- After writing code: run linters and type checkers
- After implementing logic: run the relevant tests
- After making changes: verify the full test suite still passes
- After each micro-task: confirm the system is in a known-good state

This continuous verification is what transforms a sequence of AI operations into a reliable engineering process.

## AGENTS.md as External Memory

One of the most powerful but underappreciated tools for autonomous coding agents is the external memory file—commonly named `AGENTS.md` or `CLAUDE.md` in practice.

### What Goes in External Memory

An effective external memory file contains:

**Project context**: Technology stack, architecture decisions, coding conventions
```markdown
## Stack
- Next.js 15 with App Router
- TypeScript strict mode
- Prisma ORM with PostgreSQL
- Tailwind CSS with shadcn/ui components
```

**Coding conventions**: Style rules and patterns the agent should follow
```markdown
## Conventions
- Use named exports, not default exports
- Prefer server components; use 'use client' only when needed
- All API routes must validate input with Zod schemas
- Error handling: use Result types, not try/catch for business logic
```

**Known issues and workarounds**: Things the agent shouldn't waste time rediscovering
```markdown
## Known Issues
- The Prisma client must be regenerated after schema changes: `npx prisma generate`
- Hot reload doesn't work for changes to middleware.ts—restart the dev server
- The `auth()` function is async in Next.js 15; always await it
```

**Task-specific instructions**: Guidance for common operations
```markdown
## Adding a New API Endpoint
1. Create the route file in `src/app/api/`
2. Add input validation with Zod
3. Add the corresponding test in `__tests__/api/`
4. Update the API documentation in `docs/api/`
```

### Why External Memory Matters for Autonomous Loops

Without external memory, every new agent session starts from zero. The agent needs to rediscover the project structure, conventions, and gotchas—wasting tokens and time. With a well-maintained external memory file:

- New sessions start with full project awareness
- Conventions are followed consistently across sessions
- Known pitfalls are avoided without re-learning
- Common workflows are executed correctly on the first try

### Memory That Improves Over Time

The "self-improving" aspect partly comes from updating external memory based on discoveries. When an agent encounters a new issue or figures out a better approach, that knowledge can be captured:

```markdown
## Learned: 2024-Q3
- Database migrations must be run before tests in CI
- The payment webhook handler needs idempotency checks
- Use `unstable_cache` for data that changes hourly, not `revalidate`
```

Over time, the external memory becomes a comprehensive handbook that makes each subsequent autonomous session more efficient and reliable.

## Persistent Memory Across Sessions

Beyond file-based external memory, several patterns enable agents to maintain continuity across sessions:

### Git History as Memory

The git log itself serves as persistent memory. An agent can:
- Review recent commits to understand what's been done
- Read commit messages to understand the reasoning behind changes
- Check the diff of recent changes to maintain context continuity

### Task Tracking Integration

Connecting the agent to a task tracker (issues, project boards) provides another memory layer:
- The agent knows which tasks are complete, in progress, or pending
- Failed attempts are recorded with context for future sessions
- Dependencies between tasks are visible

### Session Handoff Files

For long-running projects, creating explicit handoff notes between sessions ensures nothing is lost:

```markdown
## Session Summary - 2024-03-15
### Completed
- Implemented user profile API endpoints
- Added avatar upload with S3 integration

### In Progress
- Profile edit form (frontend) - component created but validation not working

### Blocked
- Email notification system waiting on SendGrid API key

### Notes for Next Session
- The avatar crop library has a bug with PNG transparency—use JPEG for now
- Profile validation schema is in `src/schemas/profile.ts`
```

## Task Isolation Strategies

Autonomous agents work best when tasks are properly isolated—when completing one task can't accidentally break another.

### Branch-Per-Task

The simplest isolation strategy: each task runs on its own git branch.

- The agent creates a feature branch from main
- All work happens on that branch
- If the task fails catastrophically, the branch can be discarded without affecting anything
- Successful branches are merged through the normal review process

### Environment Isolation

For tasks that involve infrastructure or dependencies:

- Use separate development databases for different agents
- Run tests in containers to prevent interference
- Use separate environment variable files for different task contexts

### Scope Limiting

Define clear boundaries for what the agent can modify:

- Specify which files and directories the task should touch
- Explicitly list files that should NOT be modified
- Use read-only access for reference files

## QA Validation Loops

The "self-improving" in self-improving agents comes largely from automated QA loops—structured verification processes that catch and correct issues.

### The Basic QA Loop

```
1. Agent makes a change
2. Run automated checks:
   - Linting (ESLint, Prettier)
   - Type checking (TypeScript compiler)
   - Unit tests
   - Integration tests
3. If all checks pass → proceed to next task
4. If any check fails → analyze failure, fix, return to step 2
5. Maximum retry limit to prevent infinite loops
```

### Progressive Validation

Not all checks need to run at every step. A progressive validation strategy runs faster checks first:

1. **Syntax check**: Does the code parse? (milliseconds)
2. **Type check**: Does it type-check? (seconds)
3. **Unit tests**: Do related tests pass? (seconds to minutes)
4. **Integration tests**: Does the system work end-to-end? (minutes)
5. **Build check**: Does the full build succeed? (minutes)

Failing early on a syntax error saves the time of running the full test suite.

### Quality Gates

Define clear quality thresholds that must be met before a task is considered complete:

- All existing tests pass
- New code has test coverage above a minimum threshold
- No new linting warnings introduced
- No type errors
- Build succeeds
- Performance benchmarks don't regress (for critical paths)

## When to Let Agents Run vs. When to Intervene

Autonomous doesn't mean unsupervised. Knowing when to let agents work and when to step in is crucial.

### Let Agents Run When

- **Tasks are well-defined**: Clear inputs, clear success criteria
- **Verification is automated**: Tests, linters, and type checkers cover the change
- **Scope is limited**: The task touches a small, well-understood part of the codebase
- **Rollback is easy**: Git branches make it simple to discard failed attempts
- **The cost of failure is low**: Internal tools, non-critical features

### Intervene When

- **Architecture decisions are needed**: Agents should implement decisions, not make them
- **Security-sensitive code**: Authentication, authorization, data access patterns
- **External system integration**: APIs, databases, third-party services
- **User-facing design**: Layout, copy, interaction patterns
- **The agent is stuck in a loop**: Repeated failures on the same step indicate a problem the agent can't solve alone

### The Review Checkpoint

Even for autonomous work, human review remains essential. The ideal flow is:

1. Agent works autonomously, producing a branch with commits
2. Agent opens a pull request with a description of what was done
3. Human reviews the changes, focusing on:
   - Architectural fit
   - Security implications
   - Business logic correctness
   - Edge cases the tests might not cover
4. Human approves or requests changes
5. If changes requested, agent addresses them in another autonomous loop

## Practical Setup Guide

### Step 1: Prepare External Memory

Create an `AGENTS.md` file with project context, conventions, and common workflows. Start minimal and expand as you discover what the agent needs to know.

### Step 2: Design Task Decomposition

Break your project into independently completable tasks. Each task should:
- Have clear success criteria
- Be verifiable with automated tests
- Take no more than 30-60 minutes of agent time
- Have minimal dependencies on other incomplete tasks

### Step 3: Set Up Automated Validation

Ensure your CI pipeline covers:
- Linting and formatting
- Type checking
- Unit and integration tests
- Build verification

### Step 4: Establish the Loop

Configure the agent to work in cycles:
1. Pick a task from the queue
2. Create a branch
3. Implement the task in small steps with verification
4. Run the full test suite
5. Create a pull request
6. Move to the next task

### Step 5: Monitor and Improve

Track metrics over time:
- Task completion rate
- Average time per task
- Number of human interventions needed
- Types of failures encountered

Use these metrics to improve your external memory, task decomposition, and validation coverage.

## The Future Is Autonomous (With Guardrails)

Self-improving coding agents aren't science fiction—they're a practical reality today with the right setup. The patterns described here—tiny steps, external memory, task isolation, and QA loops—transform unreliable AI coding from a novelty into a productive engineering workflow.

The key insight: autonomy is earned through structure. The more structure you provide (clear tasks, comprehensive tests, well-documented conventions), the more autonomy the agent can handle. Start with tight supervision and gradually expand as confidence builds.
