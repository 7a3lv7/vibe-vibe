---
title: How We Vibe Code at a FAANG
description: First-hand experience from a senior engineer at a major tech company, showing how AI-assisted development is used in production workflows
author: Anonymous FAANG Engineer
source: https://www.reddit.com/r/ClaudeAI/comments/1i4kqxo/how_we_vibe_code_at_a_faang/
date: '2025-08-01'
category: 01-core-concepts
tags:
  - Vibe Coding
  - FAANG
  - Test-Driven Development
  - Design Documents
  - Production Environment
---

# How We Vibe Code at a FAANG

**Author: Anonymous FAANG Engineer**

**Original: [Read the full post](https://www.reddit.com/r/ClaudeAI/comments/1i4kqxo/how_we_vibe_code_at_a_faang/)**

<div class="article-meta">
</div>

I wanted to share this experience because I frequently see the claim that AI-assisted development is only good for toy projects and can't make it into production. At least where I work, that judgment doesn't hold up.

A bit of background: I've been in engineering for over a decade, about half of that at FAANG or equivalent companies. The first part of my career leaned more toward systems engineering, and I gradually transitioned to software development. So I've experienced both traditional engineering processes and the shift toward incorporating AI into production workflows.

Here's the basic way we use AI in production code.

## 1. Technical Design Documents

Everything still starts with a technical design document—this is where the most upfront investment goes. It typically begins with a proposal: first explaining why something is worth doing. Only after relevant stakeholders agree on the direction does it move into formal system design, covering architecture, boundaries, dependencies, and integration plans with other teams.

## 2. Design Review

Before development actually begins, we go through design review. The goal at this stage is to surface problems as early as possible, letting senior engineers identify weak points in the design. The process may not be comfortable, but the earlier you lay problems out on the table, the lower the rework costs later.

## 3. Subsystem Documentation

After design approval, development officially kicks off. Over the following weeks, we continue breaking documentation down, writing separate subsystem specifications for each development team—clarifying interfaces, responsibilities, and delivery boundaries.

## 4. Sprint Planning and Task Decomposition

Then comes backlog planning. Developers work with PMs and TPMs to break work into discrete, executable tasks with clear priorities, dependencies, and ownership for each task.

## 5. Test-Driven Software Development

Only at this step do we actually start working on specific tickets. AI's value is primarily here: it can significantly accelerate implementation velocity, but only when the process itself is already clear. We use test-driven development, so I typically have coding agents write tests for the target functionality first, then participate in implementation. This way, task boundaries are clearer and acceptance criteria are more explicit.

## 6. Code Review and Merging

Our process requires code to be approved by at least two developers before merging to `main`. AI can assist with review, but the final sign-off still relies on the team's established review standards.

## 7. Staging Environment Validation

After code merges, it must be validated in staging first. Only when staging performs normally do we proceed to production.

---

## Overall Results

Overall, after integrating AI into this workflow, the cycle from feature proposal to production deployment shortened by approximately **30%**. For a high-standards team, that's a very significant improvement.

## TL;DR

Get the design documents and architecture right first, then proceed module by module. Write tests first, then implement. AI truly amplifies a rigorous process—it doesn't replace one.
