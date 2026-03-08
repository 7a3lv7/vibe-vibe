---
title: 'Vibe Coding: Revolution or Reckless Abandon?'
description: 'Decoding vibe coding: the hype, the hope, and the hard truths for developers'
author: Addy Osmani
source: https://addyo.substack.com/p/vibe-coding-revolution-or-reckless
date: '2026-03-07'
category: 01-core-concepts
tags:
  - AI Programming
  - Vibe Coding
  - Code Quality
  - Software Engineering
  - AI-Assisted Development
---

# Vibe Coding: Revolution or Reckless Abandon?

**Author: Addy Osmani**

**Original: [Read the full article](https://addyo.substack.com/p/vibe-coding-revolution-or-reckless)**

> ## Summary
>
> Decoding vibe coding: the hype, the hope, and the hard truths for developers

---

**Key takeaway: While AI has dramatically accelerated code creation (e.g., "vibe coding"), it dangerously lowers the bar for introducing subtle bugs, security vulnerabilities, and unmaintainable complexity. The industry needs developers to act as expert curators and critical validators, reinforcing good design, code review, security testing, and system understanding to ensure AI contributes to robust, reliable software. "Vibe coding" is not an excuse for low-quality work.**

## The Origins of "Vibe Coding"

> _"Surrender to the vibes, embrace exponential speedups, forget that the code even exists."_

In early 2025, Andrej Karpathy [introduced](https://x.com/karpathy/status/1886192184808149383?lang=en) **vibe coding** to the world with those words—a provocative new approach to writing software. Karpathy—co-founder of OpenAI and former AI director at Tesla—coined the term in a semi-joking post describing a surreal coding experience: using an AI pair programmer so advanced that he could *talk* to it via voice recognition and simply "feel" his way through a project.

He'd hand even trivial changes—UI tweaks—directly to the AI, hit "Accept All" without closely reviewing the diffs, and when errors appeared, paste the error messages back for another round of iteration. The codebase would grow at a pace far exceeding his ability to track line by line, but through constant trying and adjusting, he could push an app to a "working" state. As Karpathy himself admitted:

> _"Not bad for a throwaway weekend project... I just look at stuff, say stuff, run stuff, and copy-paste stuff, and it mostly works."_

Within weeks, *vibe coding* became a buzzword covered by the New York Times, Ars Technica, and The Guardian. It was a compelling origin story: one of the world's top AI researchers and programmers openly experimenting with ceding control to an AI coder.

## Defining the "Vibe": From Karpathy's Intent to Industry Discourse

Karpathy intended the term for a **specific** extreme: the developer is more of a prompt-giver and bug tester, while AI does most of the coding work without the developer carefully reading every line. But as the term went viral, some observers missed the wink and treated *vibe coding* as a serious methodology to be broadly applied.

Seasoned developers like Simon Willison quickly pointed out that *"vibe coding is not the same as coding with the help of LLMs."* In responsible AI-assisted development, you use tools like GitHub Copilot and ChatGPT to generate code, *but you still review, test, and integrate those results as you normally would.*

The discussion polarized. Enthusiasts used "vibe coding" to casually mean **rapid prototyping with AI**—a kind of improvisational pair programming. They emphasized it was about high-level guidance and fast iteration, not complete abdication of responsibility. Critics saw it as a dangerous practice no competent engineer should indulge in.

A nuanced consensus eventually emerged: *vibe coding refers to a very specific and risky AI-driven development pattern*—essentially "letting AI drive" while humans only nudge and observe. It is a radical subset of AI-assisted development, distinct from the disciplined use of AI tools.

## The Appeal: Why Developers Embrace Vibe Coding

> _"For low-risk projects and prototypes, why not let it rip? LLMs can generate code an order of magnitude faster than a skilled human."_

**Speed and creative flow** are vibe coding's most seductive promises. For someone with Karpathy's implementation abilities, AI's value isn't "writing code for him"—it's accelerating the trial-and-error cycle. Boilerplate generates faster, whole implementations take shape earlier, and he can explore new ideas at unprecedented speed.

Many developers describe an almost creative flow state—an improvisational rhythm between human and machine. You're not stopping to check every semicolon; you're riding a creative wave, guided by the *feeling* of seeing the program evolve in real time.

Furthermore, vibe coding hints at a **democratization of software creation**. By dramatically lowering the barrier to generating working code, it opens the door for people with ideas but without deep programming expertise. This could usher in an era of "home-cooked software"—highly personalized apps and tools built by end users themselves.

Even experienced engineers find the appeal in a well-constrained setting: it enables **high-speed exploratory programming**, similar to rapid prototyping or hackathon mode. And there are concrete success stories—independent creators rapidly shipping profitable products, developers building complex features like DSLs and database integrations with 95% AI-generated code.

## "It Works Until It Doesn't": Security and Maintainability Risks

> _"Madly hitting 'Accept All' is the equivalent of playing Russian roulette with your codebase."_

**Security** is among the most immediate concerns. Modern applications are full of traps—from SQL injection and XSS to authentication and authorization vulnerabilities. Even the strongest models don't inherently understand your business security boundaries; without clear constraints in the prompt and context, generated code can produce implementations that are *superficially reasonable but actually dangerous*.

Beyond security vulnerabilities, there are broader **code health and maintainability** issues. Karpathy's whole point was to forget the code exists and focus only on application behavior. But as he admitted, *"the code grew beyond what I'd normally be comfortable maintaining."* If you don't understand your own codebase, how can you trust or maintain it?

Multiple developers have raised the scenario: vibe coding *"works until it doesn't work, and then you have this massive codebase you're responsible for that doesn't work, you don't understand why, and you don't know if it can be fixed."*

The mole-whacking development style—prompt a feature, run code, see error, feed error back—means you're always addressing immediately visible problems rather than proactively designing for correctness. Hidden bugs and poor design choices snowball beneath the surface. Technical debt accumulates at lightning speed.

## Prototype Tool or Production Liability?

> _"Vibe coding a prototype for fun is easy. Developing a robust software solution with auth, security, best practices... is much harder, and vibe coders can't do it yet."_

Even Karpathy carefully framed his experiment as something for weekend projects where "nobody is depending on it." The **personal tools vs. production systems** split comes up repeatedly.

A wise guideline: use vibe coding for **exploration and prototypes**, but when it comes to production, apply the usual rigor. As one blog summarized:

> _"'Vibe Coding' might get you to 80% of a functional concept. But to produce something reliable, secure, and worth paying for, you need experienced humans to do the hard work that today's models can't."_

The risk is that the boundary between prototype and production can blur quickly. A "mostly works" personal project can gain users or importance, and suddenly you have a quasi-production system that was never designed for longevity. Teams need to allow more vibe coding during spike or prototype phases, but require full code review and testing cycles before anything goes live.

## Impact on Developers and Teams

Vibe coding shifts much of the programmer's daily work toward **specifying what to build** rather than crafting the implementation. This leads to quips like *"we're all product managers now."*

Some experienced developers express concern about **skill atrophy**:

> _"My worry is that if I start doing this, I'll stop engaging with the hard aspects of building something. I think my current skills would atrophy."_

This echoes a broader anxiety: over-reliance on AI assistance could make the next generation of programmers less capable of independently solving hard problems. However, the **Industrial Revolution analogy** is instructive:

> _"There are still people who make custom, high-quality goods... but most of what used to be made in workshops is now produced in factories, with orders of magnitude fewer workers."_

In software terms: routine CRUD apps might be "factory-produced" by AI with fewer human developers, but custom systems, complex systems, and novel-problem-intensive systems will still need experienced engineers. The most likely outcome is that software engineering will fork: routine application development becomes more automated, while cutting-edge and critical systems remain in the hands of highly skilled engineers who now use AI tools as force multipliers.

## Conclusion: Balancing Vibes and Rigor

Vibe coding's rise has sparked excitement, backlash, and serious discussion—but ultimately, it reinforces an old principle: **tools change; the underlying constraints of software engineering don't disappear.**

The key takeaways are about **nuance**:

**Vibe coding is neither the end of programming as we know it, nor a meaningless buzzword for lazy hackers. It's a tool and technique with specific strengths and weaknesses.** Used in the right context—hackathon projects, prototypes, personal scripts—it can significantly reduce friction and open creative doors. Used recklessly in the wrong context—security-sensitive production systems, team projects with multiple collaborators—it can invite disaster.

The appropriate motto might be: _**"Use AI to code faster, but never outsource your understanding."**_ When you accept code you don't understand, debt has already been incurred. Good engineers pay that debt back through review, testing, and structural cleanup before merging.

Perhaps the ultimate vibe should be one of harmony: humans and AI working together, each compensating for the other's weaknesses, building software faster and better than ever before. Getting there requires experimentation and discipline in equal measure—keeping room for "going by feel" while always being ready to take the wheel when AI drifts off course.

**Ultimately**, the vibe should serve the code, not the other way around. As long as that principle holds, vibe coding can be an effective method rather than a recipe for losing control.

---

**Related Reading:**

- [The 70% Problem: Hard Truths About AI-Assisted Coding](https://addyo.substack.com/p/the-70-problem-hard-truths-about)
- [Beyond the 70%: Maximizing Human-AI Collaboration](https://addyo.substack.com/p/beyond-the-70-maximizing-the-human)
