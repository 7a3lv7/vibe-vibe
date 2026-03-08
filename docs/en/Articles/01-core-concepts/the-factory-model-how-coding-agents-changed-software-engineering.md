---
title: 'The Factory Model: How Coding Agents Changed Software Engineering'
description: Software engineering is no longer about writing code—it's about building the factory that produces software. How agentic engineering is shifting developers from writing code to orchestrating systems.
author: Addy Osmani
source: https://addyosmani.com/blog/factory-model/
date: '2026-03-07'
category: 01-core-concepts
tags:
  - Agentic Engineering
  - Software Engineering
  - Abstraction Levels
  - Factory Model
  - Test-Driven Development
  - Systems Thinking
---

# The Factory Model: How Coding Agents Changed Software Engineering

**Author: Addy Osmani**

**Original: [Read the full article](https://addyosmani.com/blog/factory-model/)**

> ## Summary
>
> Software engineering is no longer just about writing code—it's about building a factory that reliably produces software.

---

Something has changed recently in [agentic engineering](https://addyosmani.com/blog/agentic-engineering/) that "feels" like the abstraction level has shifted again. This isn't the common transition of tools getting marginally better and workflows gradually evolving—it's a step change. Developers who have been writing software for decades are all describing it the same way: the center of gravity of the craft has moved.

The most useful thing you can do right now is hold two ideas in tension. **Coding has changed enormously. But the core of software engineering has not changed.** The gap between these two is where the interesting story lives, and understanding it clearly is the difference between engineers who thrive in this era and those who get left behind.

## The Arc of Abstraction

**The history of software engineering is the history of raising abstraction.** We went from bits to instructions, instructions to functions, functions to objects, objects to services, services to distributed systems. Each jump up the stack made individual developers more productive and expanded the total population who could participate in building software.

Assembly yielded to C. C yielded to managed languages and garbage collection. Managed languages yielded to frameworks, package ecosystems, and cloud infrastructure. Each transition felt disruptive at the time. In hindsight, each was just the next step in a long, consistent arc.

**What we are experiencing now is another step in that same arc. We are moving from writing code to orchestrating systems that write code.**

## Three Generations of AI Coding Tools

Being precise about this progression is helpful because confusing the generations leads to underestimating how much has actually changed.

**The first generation was accelerated autocomplete.** Tools that predicted the next line, filled in boilerplate, and saved keystrokes on repetitive patterns. Useful. Genuinely time-saving. But the workflow was identical to before: you drive, the tool assists. The feedback loop was: write code, run it, debug, repeat. AI just reduced friction within that loop.

**The second generation introduced synchronous agents.** You described a task in natural language. The model generated code. You reviewed it, corrected it, iterated to a working result. This moved further up the stack. Less typing, more describing intent. But you were still present at every step. The agent was a collaborator, not an autonomous worker.

**The third generation introduced autonomous agents.** These agents can take a spec and run for thirty minutes, an hour, hours, or increasingly days. They set up environments, install dependencies, write tests, hit failures, research solutions online, fix failures, write implementation, test again, set up services, and produce artifacts you can review. You hand them a task, turn to something else, and come back to review logs, previews, and pull requests. You are no longer interacting line by line. You are defining outcomes and reviewing deliverables.

This changes the cadence of work in a way that's hard to fully convey without experiencing it. Tasks that three months ago were weekend projects are now things you kick off and check on thirty minutes later.

## The Factory Mental Model

**The most useful mental model for this new paradigm is that you are no longer just writing code. You are building the factory that produces your software.**

This factory consists of a fleet of agents. Each agent has a task, a toolbox (repos, test runners, deployment scripts, documentation), context (specs, architectural decisions, prior constraints), and a feedback loop. Instead of hand-guiding a single agent through a single task, you are launching many agents in parallel. One handles a backend refactor. Another implements a feature. Another writes integration tests. Another updates documentation. You review output, give feedback, refine specs, and redeploy.

The analogy is deeper than it initially appears. Factories have quality control. Factories have process documentation. Factories have inputs that need to be precisely specified or the output goes wrong. When the environment is unreliable, factories stall. All of these properties map directly to agentic software development, and taking the analogy seriously will guide you toward the investments that actually matter.

## The Onboarding Parallel

One of the most striking patterns in how agents actually behave is how closely their work cycle mirrors new engineer onboarding.

You hand them a spec. They break it into subtasks. They explore the codebase to get their bearings. When they get stuck, they search commit history. They run `git blame` to find who last touched a subsystem. They escalate to appropriate humans via Slack or similar communication channels for domain knowledge, then carry on. They iterate until output meets acceptance criteria.

The cycle is familiar because this is how people work. The implication is important. Slack and email are becoming interfaces between humans and agents, not just between humans and humans. Git history is evolving into a knowledge graph that agents navigate to understand architectural decisions. Documentation is becoming training material for autonomous execution.

If you want to think clearly about what investments to make in your codebase now, ask yourself: could a new engineer, armed only with available documentation and commit history, understand why the code is built the way it is? If the answer is no, agents will also struggle there, and the leverage you could have gained will be limited.

## Your Specs Are Your Leverage

This is the insight that reshapes how you think about your value as an engineer.

If you can orchestrate twenty, thirty, fifty agents running in parallel, the difference between mediocre output and excellent output is almost entirely a function of the quality of your specs. At this scale, fuzzy thinking doesn't just slow you down. It multiplies. Vague requirements propagate through dozens of parallel autonomous runs, each going slightly wrong in slightly different directions. Bad architectural decisions made upfront don't affect one implementation. They propagate across the entire fleet.

**Unless you deeply understand architecture, integration boundaries, edge cases, failure modes, and invariants that must never break, you cannot write a spec that survives in that environment.** The spec is no longer a prompt. The spec is product thinking made explicit.

This is why strong software engineers get more leverage from these tools than weaker ones, not less. The mechanical work of inputting code is being automated. The cognitive work of understanding systems is being amplified.

## Why Testing Matters More Than Ever

Good testing and test-driven development (TDD) were already good practice. In agentic workflows, they become close to mandatory.

**Red/Green TDD** means you write tests before writing implementation. You confirm the tests fail (red phase). Then you iterate on implementation until the tests pass (green phase). This sequence is not optional ritual. It is the mechanism that gives you confidence the implementation is actually doing what you think it's doing.

For a fleet of agents generating code across dozens of parallel tasks, the costs compound seriously. **Agents that optimize for passing tests will find ways to pass them. If tests are written after implementation, they are likely to test what the implementation happens to do, not what it should do.** You now have a large surface area of code whose test suite confirms the wrong thing.

Telling agents to use red/green TDD is one of the highest-leverage instructions you can give at the start of a task.

## The Open Problem Is Verification, Not Generation

Generation is no longer the bottleneck. Verification is.

Agents can produce impressive output. The challenge is knowing with confidence whether that output is correct. Several factors make this harder than it initially appears:

- Tests that passed before a change don't guarantee they'll catch regressions introduced by the change
- Agents can write tests that are technically valid but miss important cases
- UI validation remains brittle
- Context window limits mean agents working on large codebases may miss important constraints
- Flaky environments, a minor nuisance for a single developer, become systemic blockers when forty agents hit the same flaky test simultaneously

Until verification catches up with generation, human review is not optional overhead. It is the safety system.

## High-Leverage Engineering in the New Shape

The engineers who will have the greatest impact in this era will be distinguished by a different set of capabilities:

- **Systems thinking.** The ability to hold complex architectures in mind, understand how components interact, and predict how changes in one place affect behavior elsewhere.
- **Problem decomposition.** Knowing how to break a large, ambiguous goal into well-scoped subtasks that agents can reliably execute.
- **Architectural judgment.** Understanding why a system is designed the way it is, what properties it's optimizing for, and what trade-offs were made.
- **Spec clarity.** The ability to write requirements that are unambiguous, complete with respect to important edge cases, and structured in a way that makes evaluation straightforward.
- **Output evaluation.** The taste to recognize when something looks correct but isn't, when an implementation solves the stated problem but creates new ones.
- **Orchestration skill.** The practical ability to manage multiple parallel workflows, give effective feedback on agent output, and maintain coherence across a fleet of autonomous workers.

None of these are entirely new skills. Good engineers have always needed them. What has changed is their relative importance. The mechanical parts of software development are increasingly handled by machines. The cognitive parts are being amplified.

## The Bigger Picture

New website creation is up 40% year over year. New iOS apps are up nearly 50%. US GitHub code pushes jumped 35%. All of these metrics were flat for multiple years prior to late 2024. People who have never written a line of code are building and launching software.

**The barrier to creating software has genuinely dropped. This isn't hype. For professional engineers, it means not that their skills are less valuable, but that the skills that matter have moved up the stack, as they have in every previous transition.**

The developers who will thrive in the agentic era are those who understand this as another step in the same arc and invest accordingly. Not resisting the tools. Not uncritically surrendering to them. But developing the judgment, clarity, and systems thinking that make the tools maximally effective.

Programming as primarily a keystroke activity is over. Programming as primarily a thinking and judgment activity has been accelerating for decades and just shifted into a higher gear.

The factory model isn't a metaphor about losing control of your software. It's a metaphor about building leverage. Engineers who understand this will build the most interesting things of the next decade.
