---
layout: home
hero:
  name: "Vibe Vibe"
  text: "AI Programming for Everyone"
  tagline: "Build Products Without Writing Code\nFrom Zero to Full-Stack Development, Amplify Your Creative Genius"
  image:
    src: /logo.png
    alt: Vibe Coding
  actions:
    - theme: brand
      text: Beginner's Guide
      link: /en/Basic/
    - theme: alt
      text: Advanced Track
      link: /en/Advanced/
    - theme: alt
      text: Hands-on Projects
      link: /en/Practice/
    - theme: alt
      text: Quality Articles
      link: /en/Articles/

features:
  - title: Beginner-Friendly
    details: No programming experience required. Starting from "what is code", we'll guide you step-by-step to build your first project
  - title: AI-Driven Development
    details: Learn to "command AI to write code" instead of "writing code yourself". Describe requirements in natural language and let AI implement them
  - title: MVP Mindset
    details: Master the "Minimum Viable Product" philosophy. Validate your ideas with minimal time and avoid the trap of feature creep
  - title: Modern Tech Stack
    details: Advanced track based on Next.js + TypeScript + Drizzle, learning enterprise-level full-stack development best practices
  - title: Security Awareness
    details: Build security baseline awareness from day one. Learn to protect user data and avoid common security vulnerabilities
  - title: Progressive Learning
    details: Fundamentals build cognition, Advanced dives into practice. Two paths for different stages of your journey
---

<style>
:root {
  --vp-home-hero-name-color: transparent;
  --vp-home-hero-name-background: -webkit-linear-gradient(120deg, #15a051ff 30%, #2eb3dfff);
  --vp-home-hero-image-background-image: linear-gradient(-45deg, #15a051ff 50%, #2eb3dfff 50%);
  --vp-home-hero-image-filter: blur(44px);
}

@media (min-width: 640px) {
  :root {
    --vp-home-hero-image-filter: blur(56px);
  }
}

@media (min-width: 960px) {
  :root {
    --vp-home-hero-image-filter: blur(68px);
  }
}

/* Guide text styling */
.VPHero .tagline::after {
  content: "Click the buttons below to start your AI programming journey";
  display: block;
  margin-top: 1rem;
  color: #6B7280;
  font-size: 0.95rem;
}

/* Button color configuration - individual styling for each action wrapper */
/* Button 1: Beginner's Guide - Green */
.VPHero .actions > .action:nth-child(1) .VPButton {
  background-color: #15A051 !important;
  border-color: #15A051 !important;
}
.VPHero .actions > .action:nth-child(1) .VPButton:hover {
  background-color: #4F5B53 !important;
  border-color: #4F5B53 !important;
}

/* Button 2: Advanced Track */
.VPHero .actions > .action:nth-child(2) .VPButton {
  background-color: #45523E !important;
  border-color: #45523E !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(2) .VPButton:hover {
  background-color: #45523E !important;
  border-color: #45523E !important;
}

/* Button 3: Hands-on Projects - Gray */
.VPHero .actions > .action:nth-child(3) .VPButton {
  background-color: #6B7280 !important;
  border-color: #6B7280 !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(3) .VPButton:hover {
  background-color: #9CA3AF !important;
  border-color: #9CA3AF !important;
}

/* Button 4: Quality Articles - Gray */
.VPHero .actions > .action:nth-child(4) .VPButton {
  background-color: #6B7280 !important;
  border-color: #6B7280 !important;
  color: white !important;
}
.VPHero .actions > .action:nth-child(4) .VPButton:hover {
  background-color: #9CA3AF !important;
  border-color: #9CA3AF !important;
}
</style>

## Learning Paths

<div class="paths-container" style="display: flex; gap: 2rem; margin: 2rem 0; flex-wrap: wrap;">

<div style="flex: 1; min-width: 300px; padding: 1.5rem; border-radius: 12px; background: linear-gradient(135deg, #15A05111 0%, #15A05122 100%); border: 1px solid #15A05133;">

### Fundamentals: Zero to Hero

<a href="/en/Basic/" style="display: inline-block; margin-bottom: 1rem; padding: 0.5rem 1.5rem; background: #15A051; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;">Start Reading Fundamentals →</a>

**Target Audience**: Complete beginners, liberal arts students, designers, product managers

**Learning Goals**: Understand Vibe Coding · Build your first project with AI · Master MVP mindset

**Content Preview**:
- **Awakening**: Mental shift from coder to commander
- **Philosophy**: MVP thinking, the art of not adding features
- **Techniques**: Prompt engineering, PRD writing
- **Practice**: Build your project from 0 to 1
- **Advanced**: Version control, deployment, security awareness

**Tech Stack**: HTML/CSS/JS · AI Tools (ChatGPT/Claude/Cursor) · Git · Static Deployment

</div>

<div style="flex: 1; min-width: 300px; padding: 1.5rem; border-radius: 12px; background: linear-gradient(135deg, #45523E11 0%, #45523E22 100%); border: 1px solid #45523E33;">

### Advanced: From Zero to Launch - A Pitfall Avoidance Guide

<a href="/en/Advanced/" style="display: inline-block; margin-bottom: 1rem; padding: 0.5rem 1.5rem; background: #45523E; color: white; text-decoration: none; border-radius: 8px; font-weight: 500;">Start Reading Advanced →</a>

**Target Audience**: Developers who want to understand the complete project delivery process

**Learning Goals**: Full-stack tech stack · Frontend-backend architecture · Production-grade deployment · Engineering mindset

**Content Preview** (2/16 completed, other chapters have preface available):
- **Development Basics**: Environment setup · Dev tools · AI tuning · Documentation-driven
- **UI & Data**: UI/UX · Environment variables · Databases · Test automation
- **Deployment & Ops**: Public network access · Git collaboration · CI/CD · Domain DNS · VPS deployment
- **Security & Growth**: SEO · Analytics · User feedback & product iteration

**Tech Stack**: Next.js 16 · React · TypeScript · Tailwind · shadcn/ui · Drizzle · PostgreSQL

</div>

</div>

## Project Status

::: tip Official Development Resumed
VibeVibe tutorial officially resumed development on **January 25, 2026**, with continuous updates.
:::

::: warning Internal Preview Version
- Current version is an **internal preview**, not representative of final quality
- Chapter content is being continuously optimized and refined
- If you find issues, please provide feedback via [GitHub Issues](https://github.com/datawhalechina/vibe-vibe/issues)
:::

::: tip 🎯 Claude Code Full Feature Overview
Want a comprehensive understanding of Claude Code capabilities? Visit **[cclog.vibevibe.cn](https://cclog.vibevibe.cn)** to see the complete feature breakdown from v0.2 to v2.1, covering 220+ versions, 1000+ updates, including AI models, multi-agent collaboration, MCP ecosystem, terminal experience, security sandbox, and all core capabilities.
:::

::: tip Advanced Edition Preview: Online IDE + Skills
We're launching an **online development environment** soon - no local setup required, just open your browser to start learning:

- **Cloud IDE** — Built-in complete Node.js 24, Python, Docker development environment
- **50+ AI Skills** — Pre-installed rich AI assistance skill packages covering the entire development workflow
- **Ready to Use** — No software installation needed, login and start coding practice immediately

Lowering the learning barrier to the minimum, stay tuned!
:::

## What is Vibe Coding?

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."
>
> — Andrej Karpathy, 2025

**Vibe Coding** is the hottest programming approach of 2025 (Collins Dictionary Word of the Year). Its core philosophy:

- **Describe requirements in natural language**, not hand-write code
- **Let AI generate code**, you review and adjust
- **Iterate quickly**, building something that works is more important than perfect code

Simply put: **You handle the ideas, AI handles the implementation.**

## This Course Won't Make You a Senior Programmer

- You won't master algorithms and data structures
- You won't become a framework source code expert
- You won't gain big tech interview passing ability

But you will gain:

- Ability to independently deliver products
- Methods to efficiently collaborate with AI
- Execution power to turn ideas into reality

These are two different paths, each with its own value.

## Who Should Learn This?

| Who You Are | Recommended Path | Reason |
|-------------|------------------|--------|
| Designer / Product Manager | Fundamentals | Build working prototypes with zero coding background |
| Liberal Arts / Career Switcher | Fundamentals | Start from the most basic concepts, progress step by step |
| Frontend Developer | Advanced | Expand backend capabilities, become a full-stack engineer |
| Backend Developer | Advanced | Understand modern frontend ecosystem and Next.js |
| Entrepreneur / Indie Developer | Both | Quickly build MVPs, complete products independently |
| Developers Seeking Efficiency | Fundamentals + Advanced | Systematically learn AI-assisted development workflow |

<!-- Temporarily hiding contributor info during filing period -->
For more project information, visit the [GitHub Repository](https://github.com/datawhalechina/vibe-vibe).

Need private deployment? Check out the [Deployment Guide](/deployment/) →

<!--
## Contributors List

Thanks to the following members for their contributions to this project:

| Name | Role | Bio |
| :----| :---- | :---- |
| [Fu Hangkang](https://www.hangkangfu.cn) | Project Lead & Core Contributor | An AI era native, Hunan University Class of 2024 undergraduate |
| [Qi Guohao](https://www.guohaoqi.cn) | Project Lead & Core Contributor | Currently studying at National University of Singapore School of Computing, founder of Hunan University FinTech Association |
| Liu Lei | "Articles, Practice" Contributor, Image Contributor | University of Chinese Academy of Sciences, a working backend coder, passionate about Vibe, advocates sharing. |
| Chen Junxi | "Quality Articles" Section Contributor | Current president of Hunan University FinTech Association, Hunan University School of Finance and Statistics Class of 2024 undergraduate |
| Jin Long | "Practice" Section Contributor | Deputy director of Peking University Student Innovation Society AI Club, Peking University Class of 2024 graduate student |
| Shu Lulu | "Practice" Section Contributor | Founder of Hunan University "Lushan Humanities+", Hunan University Yuelu Academy Class of 2023 undergraduate |

## How to Contribute

This project welcomes community contributions:

- Provide feedback in the comment section at the bottom of pages
- Submit [GitHub Issues](https://github.com/datawhalechina/vibe-vibe/issues)
- Submit Pull Requests to improve content
-->

---

<div style="text-align: center; color: var(--vp-c-text-2); font-size: 0.85rem; padding: 2rem 0; white-space: nowrap;">
  <a href="https://beian.miit.gov.cn/" target="_blank" style="color: inherit; text-decoration: none;">蜀ICP备2024097797号-3</a>
  <span style="margin: 0 8px;">|</span>
  <a href="https://beian.mps.gov.cn/#/query/webSearch?code=51170202000484" target="_blank" style="color: inherit; text-decoration: none; display: inline-flex; align-items: center; justify-content: center;">
    <img src="/gonganbeian.png" style="width: 15px; margin-right: 4px; vertical-align: middle;"/>川公网安备51170202000484号
  </a>
</div>
