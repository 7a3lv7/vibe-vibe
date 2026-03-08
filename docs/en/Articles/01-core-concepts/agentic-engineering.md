---
title: Agentic Engineering
description: Agentic engineering is an AI-assisted development approach emphasizing human oversight and engineering discipline, distinguishing constrained collaboration from casual vibe coding
author: Addy Osmani
source: https://addyosmani.com/blog/agentic-engineering/
date: '2026-03-07'
category: 01-core-concepts
tags:
  - AI-Assisted Development
  - Agentic Engineering
  - Software Engineering
  - Best Practices
  - Engineering Discipline
---

# Agentic Engineering

**Author: Addy Osmani**

**Original: [Read the full article](https://addyosmani.com/blog/agentic-engineering/)**

A year ago, Andrej Karpathy coined "vibe coding" to describe a very freeform approach to programming: you give prompts, hand the keyboard mostly to AI, and iterate through continuous feedback to rapidly advance a prototype or MVP.

The label caught on because it described a real phenomenon. But the problem is that "vibe coding" was increasingly used too broadly: from weekend projects to serious engineering workflows, people were using the same word. These two are not the same thing. The former emphasizes rapid experimentation; the latter emphasizes using AI to complete implementations within explicit constraints. Conflating them only makes the discussion more unfocused.

## What Vibe Coding Actually Is

Vibe coding's most distinctive feature is **moving fast by feel** and **not prioritizing code review**. You give prompts, accept results, run and see; if something's wrong, you feed back and try again. It's closer to a high-speed prototyping flow than a complete engineering process.

This approach genuinely has value in several scenarios:

- **Building an MVP, prototype, or hackathon demo from scratch.** The goal is to get something working, not to solve long-term maintenance all at once.
- **Personal scripts and one-off tools.** You're the sole user, and the tolerance for error is wider.
- **Learning and exploration.** Newcomers can get a system running first, then understand implementation details through the process.
- **Creative divergence.** You can have AI generate multiple directions at once, then select the one worth refining.

If vibe coding enables more people who otherwise couldn't build custom software to bring their ideas to life, that's a good thing. It has a clear place in the toolbox.

But its limitations are equally clear: demos usually look great, but the problems surface when you enter the phase of modification, extension, hardening, and maintenance. The system may run, but it lacks clear structure, and the cost of subsequent iterations rises quickly.

## We Need a More Precise Term for the Professional Version

The reality is that many experienced engineers have already achieved enormous productivity gains with AI—while still maintaining code quality. But their way of working doesn't resemble the casualness that "vibe coding" implies.

They write specs before giving tasks; they review diffs line by line; they run tests; they keep architecture, quality, and maintainability firmly in their own hands. AI handles acceleration of implementation; humans define constraints and judge results.

Andrej Karpathy later suggested using **"[agentic engineering](https://x.com/karpathy/status/2019137879310836075)"**, which is more accurate. It works for roughly three reasons:

**It describes what's actually happening.** You're orchestrating AI agents capable of executing, testing, and modifying code, while you yourself assume the architecture, review, and decision-making role. You may write only a small amount of code by hand, but the entire process is always governed by your engineering constraints.

**It's clearer in professional contexts.** "Agentic engineering" sounds like a serious engineering practice: involving autonomous execution capability while emphasizing engineering discipline. It's suitable for team methodology documents and discussions about role competencies.

**It draws a clear boundary.** Vibe coding emphasizes rapid experimentation; agentic engineering emphasizes that AI handles implementation advancement while humans are responsible for architecture, quality, and correctness. Both can be valuable, but they shouldn't be conflated.

## What Agentic Engineering Looks Like in Practice

The workflow isn't mysterious, but it does require placing engineering discipline back at center stage.

**Start with a plan.** Before doing anything, write a design document or specification—even if that document itself is produced with AI assistance. Break the work into tasks with clear boundaries, think through system structure first, then enter implementation.

**Guide first, then review.** You hand well-scoped tasks to AI agents for execution. After they generate code, you review it as you would a team member's PR. If there's an implementation you can't explain clearly, it shouldn't go directly into the codebase.

**Make tests the trunk, not a patch.** One of the biggest differences between agentic engineering and vibe coding is whether there's a stable verification loop. With reliable tests, AI can iterate toward clear objectives; without tests, "done" is just a subjective judgment. Tests aren't nice-to-have—they're the key to making the collaboration process reliable.

**You always own the codebase.** Documentation must be maintained, version control must be clear, CI must be reliable, and production must be observable. AI can accelerate progress, but system responsibility doesn't automatically transfer.

## The Skill Gap We've Discussed

An uncomfortable but necessary truth: agentic engineering tends to amplify the advantages of senior engineers. If you already understand system design, security boundaries, and performance tradeoffs, AI becomes an extremely powerful lever, because you have the ability to quickly judge what's a good implementation and what's only superficially functional.

Conversely, if foundational skills aren't yet solid and most implementation is fully outsourced to AI, the risks are clear: you might produce features faster, but you may not truly understand what you've delivered. In the long term, this erodes control over the system's essence.

This isn't an argument against AI-assisted development, but a reminder: agentic engineering hasn't made engineering itself "easier"—it just moved where the hard parts are. You spend less time typing code by hand, but more time defining tasks, organizing context, reviewing results, and establishing verification mechanisms. Foundational capabilities therefore aren't becoming less important; they're becoming more important.

## Where We Go from Here

The trend is clear: AI agents will become increasingly capable, and agentic engineering will increasingly become the default working mode for professional developers.

What truly matters going forward is at least three things:

- **Use more precise terminology.** When you need to refer to rigorous, human-supervised AI collaboration, call it agentic engineering; when you need to refer to lightweight, rapid, prototype-oriented processes, call it vibe coding. Stop mixing both into one word.
- **Build better evaluation frameworks.** What we need to judge isn't just "is it faster?" but "is it reliably producing solid software?"
- **Continue investing in foundational capabilities.** As AI takes over more implementation work, architectural thinking, security awareness, system design, and verification skills will only become more valuable, not less.

The rise of AI coding hasn't weakened the craft of software engineering—it has raised the bar. Those who will truly stand out aren't the ones who prompt the fastest, but those who most clearly understand what they want to build and why. Then they bring AI agents into that clear methodology and get things right.

Vibe coding showed what happens when constraints are fully removed.

Now it's time to put "engineering" back at the center.

---

_Addy co-authored a new book with O'Reilly, [Beyond Vibe Coding](https://beyond.addy.ie/), that more systematically discusses the practice frameworks for AI-assisted engineering and agentic engineering._
