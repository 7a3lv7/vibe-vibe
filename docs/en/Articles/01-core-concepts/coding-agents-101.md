---
title: 'Coding Agents 101: The Art of Actually Getting Things Done'
description: Practical guide from the Cognition team covering prompt techniques, workflow integration, large task delegation, automation, and performance optimization
author: Cognition Team
source: https://devin.ai/agents101#introduction
date: '2025-06-15'
category: 01-core-concepts
tags:
  - Coding Agents
  - Devin
  - Prompt Engineering
  - Workflow Automation
  - AI-Assisted Development
---

# Coding Agents 101: The Art of Actually Getting Things Done

**Author: Cognition Team**

**Original: [Read the full article](https://devin.ai/agents101)**

<div class="article-meta">
</div>

It's 2025. **Coding agents aren't magic, but they're already changing how many teams work.** We've noticed that some engineers—especially senior ones—tend to figure out efficient usage patterns faster. The following lessons come from customer practice and our own experience building Devin.

## About This Guide

- **Product-agnostic:** The methods discussed here apply to most coding agents.
- **Tactical:** We aim to give advice you can act on immediately.
- **Technical:** While coding agents provide value across many roles, this guide is primarily written for engineers.

Developer tools have been evolving rapidly. A decade ago, the mainstream was autocomplete and IntelliSense. Four years ago, Copilot and tab completion became popular. Two years ago, generative chat entered development workflows. Today, autonomous agents can take a single requirement and push it forward to a reviewable PR with significantly less human intervention.

The human-AI partnership is inherently stronger than AI alone. Autonomous agents' ability to handle tasks end-to-end raises the ceiling for parallel work significantly. Often, you're more like an orchestrator of multiple tasks rather than the person writing every line of code.

## Prompt Fundamentals

These basic guidelines will help you work more effectively with coding agents in 2025. If you can only remember one thing, at least remember these.

### Tell the Agent HOW, Not Just WHAT

Think of the agent as a coding partner who needs explicit context. For simple tasks, describing the goal is usually sufficient. But as tasks grow complex, explaining your preferred approach from the start will significantly improve success rates and reduce your code review burden.

**Example:** Don't just say "add unit tests"—specify which functionality to test, identify important edge cases, and clarify what needs to be mocked (if anything).

### Tell the Agent Where to Start

Think about where you'd start if you were handling the task yourself. Even if you don't know the exact file or function name, mention the repo, relevant docs, and key components involved.

**Example:** "Please add support for Google models to our code. You should look at the latest docs and create a new implementation file in the model group directory."

### Practice Defensive Prompting

Run your prompt through your head first: if you gave the same instructions to someone who just joined the project, where would they most likely misunderstand? Fill in those potential ambiguities upfront.

**Example:** "Please fix the C++ bindings for our search module to pass the new unit tests. Be careful—you may need to recompile the bindings after each code change before testing."

### Give Access to CI, Tests, Type Checkers, and Linters

Much of agents' magic comes from their ability to fix their own errors and iterate based on error messages. Providing strong feedback loops through type checkers, linters, and unit tests greatly enhances performance. Consider using typed Python instead of plain Python, or TypeScript instead of JavaScript.

## Using Agents in Your Workflow

Once you've mastered the basics of talking to agents, it's time to bring these AI assistants into your daily workflow.

### Accept New Tasks Immediately

Imagine a teammate messages you: "Hey, can we quickly build X?" Instead of interrupting your flow, send a quick prompt to an autonomous agent. Many teams tag @Devin on Slack when discussing bug fixes or minor feature updates.

### Code from Anywhere

Autonomous agents typically support mobile access. Whether through Slack's mobile app or a dedicated mobile app, many agents let you address problems on the go.

### Hand Off Your Chores

Stuck doing git bisect on old commits or updating docs for a new feature? Delegate these repetitive tasks to your agent, freeing up your time for creative and high-impact work.

### Skip Analysis Paralysis

Stuck choosing between two architectures? Have your agent implement both options. Comparing concrete examples makes decisions straightforward, and discarding one solution doesn't hurt anyone's feelings.

### Set Up Preview Deployments

Set up CI/CD pipelines that automatically create preview deployments for each new PR. This is especially handy when reviewing frontend tasks completed by coding agents.

## Delegating Larger Tasks

When a PR's scope and complexity exceed a few files, the real leverage comes from delegating medium-to-large tasks (typically 1–6 hours) to autonomous agents. Here, you're saving hours, not minutes.

### Automate Your First Draft

For important tasks, having an autonomous agent generate a first-draft PR can kick things off quickly. The key: clearly communicate your expected approach upfront. Think of yourself as onboarding a highly capable team member—the clearer the direction, the less rework later.

| Domain | Drafting | Polishing |
|:-------|:---------|:----------|
| Journalism | Reporter gathers initial information, writes first draft | Editor reviews, fact-checks, refines, and finalizes |
| Restaurant | Prep cook prepares ingredients and initial dishes | Sous chef adjusts flavoring, then serves |
| Coding | Autonomous agent starts from plan, creates draft solution | Human developer reviews, provides feedback, adds polish before merge |

Remember: bigger tasks are far from "fully automatic hands-off." A more realistic goal is saving 80% of time, not 100%—final validation and quality are still on you.

### Co-Develop PRDs

For complex or ambiguous tasks, working with the agent to refine the plan first is often more effective than jumping straight into code. Have the agent explore: "How does our auth chain work?" or "What services might this change impact?"

### Set Up Checkpoints

For multi-part tasks, establish clear checkpoints:

Plan → Implement chunk → Test → Fix → Checkpoint review → Next chunk

Explicitly request pauses at each major stage, especially for complex features built across multiple layers (e.g., database, backend, frontend).

### Teach Self-Verification

When giving feedback, don't just point out problems. Clearly express your testing process so the agent can independently verify future tasks.

### Add Test Coverage at AI Hotspots

Agents can't yet exhaustively test every scenario interactively. Enhancing test coverage in areas heavily modified by AI ensures greater confidence in agent output.

## Automation Workflows

Agents can respond to incoming events much faster than humans, and they're more willing to do boring, repetitive work.

### Create Shortcuts for Repetitive Work

Common candidates for agent automation:
- Feature flag removal
- Dependency upgrades
- Fixing and adding tests on new feature PRs

### Smart Code Review

While specialized tools like Greptile and CodeRabbit exist for quick code reviews, autonomous agents can provide more accurate insights—especially if they've already indexed your repo's functionality.

At Cognition, we maintain a list of engineers' most common mistakes and commit it to the codebase. Instead of writing traditional lint rules (often impossible), we run agents on every new PR to check for these errors.

### Hook Into Events and Alerts

Set up autonomous agents to trigger automatically in response to specific events. These setups work especially well with MCPs that can ingest third-party error logs.

⚠️ For production issue triage, AI's debugging skills aren't great yet. Rather than asking AI to fix bugs end-to-end, it's more practical to have it flag the most suspicious errors.

## Customization and Performance

### Environment Setup

Nothing slows an agent down more than an incomplete or mismatched environment. Align the agent's setup with your team's: language versions, package dependencies, automated checks, pre-commit hooks, environment configuration, and browser logins.

### Build Custom CLI Tools and MCPs

MCPs are widely available and quick to set up. But many overlook setting up simple CLI scripts for agents—like a script that extracts Linear ticket info by ID, or one that reliably restarts the local dev environment.

One client had great success with a CLI tool that only shows the first failing test in the test suite, prompting the agent to focus on that single test with detailed error info. This led to higher success rates and faster completion on long tasks.

### Add to the Agent's Knowledge Base

If your agent makes common errors, encode your feedback into its knowledge. Many products offer .rules files or .md files for permanent ingestion. Tell it about your project's overall architecture, common test types for different task categories, how to run important commands, and which tools you recommend.

## Practical Considerations

### Limitations of Autonomous Agents

- **Limited debugging skills:** Bug reports often look straightforward, but root-cause analysis requires database, log, and full system context.
- **Poor fine-grained visual reasoning:** Current models struggle with matching design screenshots at the detail level needed. Use a good design system with reusable components.
- **Knowledge cutoff:** Whenever you want to use a new library, explicitly point to the latest documentation. Otherwise, agents will assume old patterns from their pretraining data.

## Managing Time and Minimizing Losses

Not every agent interaction succeeds. Part of the work involves learning how to maximize success while minimizing wasted time and tokens.

- **Be willing to cut losses early.** If the agent is going off track, stop the conversation or take over manually.
- **Diversify your experiments.** Try a range of different prompts and ideas. Double down on task types where agents naturally perform well.
- **Restart when stuck.** Starting a fresh session with complete constraints is often more efficient than repeatedly patching a derailed conversation.

## Security and Permissions

- **Create dedicated accounts** for your agents. Use disposable emails for testing, and custom IAM roles for cloud resources.
- **Give agents a dev/staging environment.** Avoid giving access to production services entirely.
- **Use read-only API keys** wherever possible.

## Major Changes Are Coming

We firmly believe software engineers aren't going away. Even as coding agents become smarter and more capable, deep technical expertise and thorough understanding of codebases remain irreplaceable. True ownership and verification of projects, systems, and code are more critical today than ever.

As automation amplifies your impact, the ability to handle parallel tasks simultaneously won't just become possible—it will become necessary.

---

**Original link:** [Coding Agents 101](https://devin.ai/agents101#introduction)

**Source:** Devin (Cognition AI)

**Published:** June 2025
