---
title: Vibe Coding Is Not an Excuse for Low-Quality Work
description: A practical guide to responsible AI-assisted development
author: Addy Osmani
source: https://addyo.substack.com/p/vibe-coding-is-not-an-excuse-for
date: '2026-03-07'
category: 01-core-concepts
tags:
  - AI-Assisted Development
  - Code Quality
  - Engineering Practices
  - Technical Debt
  - Code Review
---

# Vibe Coding Is Not an Excuse for Low-Quality Work

**Author: Addy Osmani**

**Original: [Read the full article](https://addyo.substack.com/p/vibe-coding-is-not-an-excuse-for)**

> ## Summary
>
> A practical guide to responsible AI-assisted development

---

**"Move fast and break more things."**

This twist on Silicon Valley's old motto has been echoing through engineering circles as "vibe coding" enters the conversation. **Yes, AI-assisted development is changing how we build software, but it's not a free pass to abandon rigor, review, or craft.** Vibe coding is not an excuse for low-quality work.

Let's first acknowledge the positive: AI-assisted development genuinely changes how software is produced. It lowers the implementation barrier, enabling newcomers and non-developers to generate working software through clear requirement descriptions. This unlocks creativity—more people can solve their specific problems with custom software. For experienced engineers, it brings clear productivity gains as well.

However, as any seasoned engineer will tell you, **speed is meaningless if the wheels fall off on the road.** This is where cracks begin to show—in the gap between the *vibe* and the *reality* of building maintainable, robust software.

## The Hard Truth: Quality Isn't Automatic

Despite the excitement, vibe coding still draws skepticism from many senior developers. The core criticism: **faster implementation doesn't automatically mean quality is met.** If AI-generated results are merged into codebases without review, technical debt gets amplified in lockstep. The half-joking line—**"two engineers can now produce the technical debt of fifty"**—resonates because it captures exactly this risk.

Many early vibe coding projects look "working" on the surface but harbor a string of issues: insufficient error handling, unconsidered performance boundaries, lax security practices, and brittle logic structures. This is what some call **"house of cards" code**—it looks complete, but starts wobbling under real-world pressure testing.

These risks aren't just theoretical. Maintainability suffers when AI-generated modules have opaque structure and excessive coupling. Security gaps emerge when prompts and context don't clearly specify boundaries, error handling, and input constraints. **The risk isn't "AI is dumb"—it's that humans relax their verification after a few smooth experiences.**

## Keeping Humans in the Loop

The most important mindset shift for effective vibe coding: **treat AI as a high-speed implementation collaborator, not a responsibility recipient.** You—the engineer, reviewer, or responsible party—still own the results. AI can rapidly produce a first draft, but whether it enters the codebase depends on whether you understand, validate, and approve it.

Experienced developers who successfully integrate AI intuitively follow this pattern. When an AI assistant suggests code, they don't just click "Accept" and move on. Instead, they:

- **Read and understand** what the AI wrote, confirming they can explain its structure and intent
- **Refactor** monolithic or messy AI output into clean, modular parts
- **Add missing edge case handling**—null checks, input validation, error conditions
- **Strengthen types and interfaces**, converting implicit assumptions into explicit contracts
- **Question the architecture**—did the AI use an inefficient approach? Introduce unnecessary global state?
- **Write tests** (or at least manually test behavior)—treat AI code like any colleague's PR: no merge without testing

By doing this, you inject engineering judgment into AI-generated code. The combination's power: AI provides speed, humans provide boundaries, validation, and tradeoffs.

**A key rule that should rarely have exceptions: unreviewed AI code should not be merged into the codebase.**

Another best practice: **let humans control design.** Use AI for implementation, not for deciding fundamental architecture. Let AI handle the *grunt work*, not the *brain work*.

**Documentation** becomes especially crucial. If AI generates a non-trivial algorithm or uses an unfamiliar library, take time to document *why* that solution was chosen. Some teams even document the prompts used to generate important code.

**Human oversight isn't "nice to have"—it's mandatory.** Once you remove humans from the loop, you're rolling dice on software quality.

## Seven Quality Rules for Vibe Coding

Let's make this concrete with actionable rules for teams adopting AI-assisted development. Think of these as the *"move fast, but don't break everything"* playbook.

**Rule 1: Always review AI-generated code—no exceptions.** Whether it comes from Copilot, ChatGPT, Cursor, or any other AI tool, it must be reviewed before entering the codebase. If you don't have time to review it, you're not ready to accept it.

**Rule 2: Establish coding standards and follow them.** Define your team's style guide, architecture patterns, and best practices. Ensure all AI output is refactored to comply. Consider creating linting or static analysis checks that specifically target common AI mistakes.

**Rule 3: Use AI for acceleration, not autopilot.** Great uses: generating boilerplate, scaffolding components, translating between languages, drafting algorithms from pseudocode. Risky uses: having AI design your modules from scratch with minimal guidance, or generating code in domains you don't understand.

**Rule 4: Test, test, test.** Generation speed doesn't automatically bring correctness. Critical paths must have tests. Even if AI helps write tests, don't rely solely on AI-generated tests—the same blind spots can appear in both implementation and tests. For user-facing features, do manual validation too.

**Rule 5: Iterate and refine.** If the AI's first output isn't satisfactory, don't accept it. Vibe coding is an iterative conversation. Prompt for improvements, or take the draft and refactor it yourself.

**Rule 6: Know when to say no.** Sometimes vibe coding isn't the right tool. If you're working on a critical security module, you may want to carefully design it by hand. If AI keeps generating complex solutions for simple problems, stop and write it yourself. **Don't let "AI did it" become an excuse for not understanding your own code.**

**Rule 7: Document and share knowledge.** Ensure any AI-contributed code is documented at least as thoroughly as handwritten code. If there are non-obvious decisions, add comments. In team discussions, be transparent about what was AI-generated—this helps reviewers give those sections extra attention.

## When Vibe Coding Works (and When It Fails)

It's equally important to recognize **where vibe coding shines and where it doesn't.**

**👍 Good Use Cases:**

- **Rapid prototyping** — Perhaps vibe coding's sweet spot. Great for validating ideas quickly where rough code is acceptable.
- **One-off scripts and internal tools** — Low risk; if a script crashes, it's not the end of the world.
- **Learning and exploration** — Generating examples to accelerate learning in new languages or APIs.
- **Structured, boilerplate-heavy tasks** — Creating 10 similar data classes, implementing a routine CRUD layer. AI excels at this mechanical repetition.

**👎 Less Suitable Use Cases:**

- **Enterprise-grade software and complex systems** — Deep business logic, heavy concurrency, strict security, or compliance requirements aren't suited for fully trusting AI output.
- **Long-term maintainability** — A hodgepodge of AI-generated code can be a poor foundation for a codebase that will exist for years with many developers.
- **Critical algorithms or optimizations** — AI may produce something that works at small scale but falls apart under load.
- **Auditability and clarity** — When code needs to be easily read and reasoned about by others (or auditors), AI's sometimes verbose or oddly abstract output may hinder clarity.

In short, *vibe coding is a powerful accelerator, but not a silver bullet.* Use it where speed matters more than polish and where you have room to iterate. **Avoid using it as a one-shot solution for mission-critical software.**

## Conclusion: Embrace the Vibe, Respect the Craft

Vibe coding and AI-assisted software development represent an exciting leap in our tooling. It **is here to stay** and will only grow more sophisticated. Forward-looking engineering teams shouldn't ignore it—those who effectively harness AI will likely outpace those who don't.

The message of this article is *not* to reject vibe coding, but to **approach it with open eyes and full engineering discipline.**

The biggest takeaway: **speed without quality is meaningless.** Shipping buggy, unmaintainable code faster is a false victory—you're just accelerating toward a cliff. The best engineers balance both: using AI to move faster *without* breaking things.

For tech leads and engineering managers, the call to action is clear: set the tone that AI is a tool to be used *responsibly*. Encourage experimentation with vibe coding, but also establish expectations—mandatory code reviews for AI-generated contributions, a culture welcoming "does this make sense?" questions, and investment in upskilling teams to work effectively *with* AI.

The fundamental principles—clear thinking, understanding requirements, designing for change, thorough testing—remain as important as ever, if not more so.

Perhaps the spirit should be: **"Move fast, but don't break things—or if you do, make sure you know how to fix them."** Leverage the vibe to code at light speed, but back it with a solid foundation of engineering excellence. AI can coexist with craft; in fact, in the hands of a craftsperson, it can be a powerful chisel. But it still takes the craftsperson's hand to guide that chisel to create something truly lasting and well-made.

So, developers, keep vibing—just carefully. Embrace the future, but don't abandon the principles that got us here. **Vibe coding is not an excuse for low-quality work**; rather, it's an opportunity to elevate what we *can* achieve when we pair human judgment with machine generation capability. Teams that internalize this won't just move fast—they'll build things worth keeping.

Happy coding, and keep the vibes high *and* the quality higher.
