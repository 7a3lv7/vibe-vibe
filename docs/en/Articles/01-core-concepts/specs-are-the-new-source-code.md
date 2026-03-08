---
title: Specs Are the New Source Code
description: How product specifications are transforming from disposable paperwork into the true source of truth in AI-powered development
author: Ravi Mehta & Danny Martinez
source: https://blog.ravi-mehta.com/p/specs-are-the-new-source-code
date: '2025-07-31'
category: 01-core-concepts
tags:
  - Product Management
  - Spec-Driven Development
  - AI-Assisted Development
  - Vibe Coding
  - Product Execution
---

# Specs Are the New Source Code

**Author: Ravi Mehta & Danny Martinez**

**Original: [Read the full article](https://blog.ravi-mehta.com/p/specs-are-the-new-source-code)**

<div class="article-meta">
</div>

## Prototypes Are the New Specs, Specs Are the New... Source Code?

Throughout my career, specs have gotten shorter and shorter. When I started at Microsoft, a PM's value was measured by the weight of their specs. A few years later at Tripadvisor, we mythologized the PM who brought a spec—written on a napkin—to a product review.

Product teams often treated specs as paperwork—a necessary evil before "real work" gets shipped. Engineers were celebrated for elegant code, designers for beautiful UX. PMs got credit for shipping impact. But specs? They were usually rushed through, discarded, forgotten.

That was always a mistake. In my [Product Competency Toolkit](https://www.ravi-mehta.com/product-manager-skills/), Feature Specification ranks first—the first of twelve competencies—for good reason. It underpins product execution, along with product delivery and product quality.

Flawless execution is the foundation of good product management, and great specs are the starting line.

## Why PMs Are Suddenly the Bottleneck

In his recent talk [Building Faster with AI](https://www.youtube.com/watch?v=RNJCfif1dPY), Andrew Ng noticed an unprecedented trend:

> "For the first time in my life, I have a manager proposing to me to have twice as many PMs as engineers. I still don't know if that proposal is a good idea, but I think it's a sign of where the world is going." —Andrew Ng

As engineering delivery accelerates with AI, companies **need more PMs to support those productive engineers, not fewer**. The pressure falls on one artifact—the spec.

## The Spec—New Source Code

In traditional software development, programmers write human-readable "source code" that gets compiled into highly optimized, machine-readable "object code." The object code (also known as binaries) is an artifact that can be recreated from source. Source code is **literally** the source of truth.

Sean Grove from OpenAI has a provocative argument. In his recent talk, [The New Code](https://www.youtube.com/watch?v=8rABwKRsec4), he argues that a well-written prompt (i.e., a spec) is the new source code.

From this perspective, we're doing AI development backwards. We carefully craft prompts to convey our intent to models. The AI generates code. Then we keep the code and discard the prompts.

"This feels like you shredded the source code, and then very carefully version-controlled the binaries," Grove observes.

Think about that. In traditional programming, source code is sacred—it contains comments, structure, and documentation. But with AI, we've flipped the relationship. We treat generated code as the artifact worth keeping, while treating the spec—the prompt—as disposable.

Grove argues this is entirely wrong. Code, even elegant code, is what he calls a "lossy projection" from the spec. Just as decompiling a binary won't give you the original comments and variable names, reading code won't tell you the full intent behind it.

Yet the spec contains everything. A sufficiently powerful spec can generate "good TypeScript, good Rust, server, client, documentation, tutorials, blog posts, and even podcasts."

> "A sufficiently powerful spec can generate good TypeScript, good Rust, server, client, documentation, tutorials, blog posts, and even podcasts." —Sean Grove, OpenAI

More importantly, specs do what code cannot: they align humans and machines on a shared goal. As Grove puts it: "A written spec effectively aligns people, it is the artifact you use to communicate, discuss, debate, and synchronize."

The new scarce skill isn't coding. It's writing specs that fully capture intent and value.

## Wait, Didn't Prototypes Kill Specs?

The entire product-building approach has recently undergone a major shift, and in this new world, **specs are often the output, not the input.**

Today, you don't need to ship a single line of code to get a prototype in front of customers. Tools like v0, Lovable, and Replit let you build working prototypes in hours rather than weeks.

- **Old workflow:** Vague idea → wireframes → design → engineer-built MVP → customer feedback → painful spec revision → rebuild → pray.
- **New workflow:** Vague idea → rapid prototype → customer feedback → crystal-clear spec → AI-assisted implementation.

Prototypes didn't kill specs. They made specs better.

## Spec-Driven Development in Action

Danny Martinez is the founder of decimals, a platform (in stealth) for expert influencers to place talent from their networks into jobs. Danny walks us through their spec-driven development process, which had two impacts:

1. Dramatically improved communication with the engineering team for larger features
2. Enabled Danny—despite no prior coding experience—to go directly from detailed specs to live features for smaller items

Here's his setup for a feature implemented in about minutes:

- **Project management:** [Linear](https://linear.app/homepage)
- **IDE:** [VS Code](https://code.visualstudio.com/)
- **Extension:** [GitHub Copilot Pro](https://code.visualstudio.com/docs/copilot/overview)
- **Model:** [Claude Sonnet 4](https://claude.ai/login?returnTo=/?)
- **MCP Server:** [Linear MCP Server](https://linear.app/changelog/2025-05-01-mcp) for accessing Linear tickets through Copilot

The workflow steps:
1. Generate a Linear ticket from a co-founder's Slack message
2. Clarify desired copy in the ticket
3. Open Copilot and prompt Claude to open the Linear ticket
4. Prompt Claude to review the ticket and analyze it relative to the codebase
5. Prompt Claude to create a branch and implement changes
6. Test changes to ensure they work as expected
7. Open a PR on GitHub
8. Wait for engineer review/approval

The real power: a non-technical person can now move between Linear tickets, the codebase, and engineers—all by sending a few prompts to Claude through GitHub Copilot.

### Three Conditions for Success

1. **Be specific:** Vague specs lead to messy codebases. Use Claude to get a first draft of the ticket, review the codebase, and help make it more concrete.
2. **Be selective:** Use this approach for simpler tasks. The more complex the ticket, the more it needs someone who knows what they're doing.
3. **Gate-keep:** This method works because there's an actual engineer reviewing changes and ensuring the balance between simplicity and functionality is maintained.

## Long Live the Spec!

William Gibson, the science fiction writer who coined "cyberspace," once said:

> The future is already here—it's just not very evenly distributed.

Today, AI's gains are profoundly uneven. Some things—generating code, text, images—have achieved quantum leaps. They run at **AI speed**. Others—talking to customers, discovering their needs, persuading them to buy—still move at **human speed**.

This uneven distribution is reshaping product teams. The spotlight is moving from implementation work to understanding work. The core skills that have always distinguished the best PMs—understanding user needs, defining problems clearly, designing elegant solutions—have become exponentially more valuable.

The best PMs translate those insights into specs—aligning teams, guiding implementation, and serving as the enduring artifacts of an increasingly automated development world.
