---
title: "5.6 Making AI Remember Your Design System"
description: "From constant reminders to set-and-forget—using project documentation and Skills to constrain AI's design output"
---

# 5.6 Making AI Remember Your Design System

> **Goal of this section**: Understand how to make AI automatically follow design guidelines through project documentation (DESIGN_SYSTEM.md) and community Skills, achieving design consistency.

---

## Xiaoming's New Frustration

After building a few pages with shadcn/ui, Xiaoming discovered a problem.

The first time he asked AI to create a login page, he said "use shadcn/ui components," and AI did exactly that. The second time, when creating a user list page, he had to say "use shadcn/ui components" again. The third time, the fourth time... he had to repeat this every single time.

What was more frustrating was that AI would sometimes "forget." The previous page used a blue theme, but this page suddenly turned purple. They had agreed on 8px button border radius, but AI gave 12px this time. He had to watch over it like a supervisor, afraid it would go off-script again.

The old-timer saw his struggle: "The problem you're facing is essentially that **AI has no memory**. Every conversation starts from scratch. You need a way to make AI remember your design system once and for all, so you don't have to keep reminding it."

---

## The Design System Dilemma

Xiaoming thought back and realized he didn't actually have a clear design system. He was just adjusting by feel—make this button rounder, that card squarer; use blue here, green there. The resulting pages looked okay individually, but put together they seemed messy, as if made by several different people.

The old-timer said: "This is the consequence of not having a design system. You need to define the rules first, then make AI follow them."

**What is a design system?** Simply put, it's a set of standards about "what things look like." For example:

- **Colors**: Which blue is primary? Which green for success? Which red for errors?
- **Spacing**: How far apart should components be? How much padding?
- **Border radius**: What radius for buttons? What about cards?
- **Shadows**: Should cards have shadows? How deep?
- **Typography**: How large for headings? For body text? Bold or regular?

These details may seem trivial, but they're exactly what determines whether your product looks "professional" or "amateur." Large companies' design teams spend months refining these standards, then enforce them on all designers and developers.

But you're working alone, without a design team. What can you do?

---

## From "Constant Reminders" to "Set-and-Forget"

The old-timer told Xiaoming there's a way to make AI automatically follow design guidelines without reminding it every time—**write the guidelines as a Skill**.

Remember Skills from Chapter 2? They're essentially "instruction manuals for AI." You can write your project's design guidelines, component usage methods, and constraints into a Skill, then tell AI to load it. From then on, AI will automatically generate code according to your standards.

Xiaoming was a bit confused: "But I don't know how to define design guidelines."

The old-timer smiled: "You already have them."

---

## You Already Have a Design System

The old-timer had Xiaoming open the `tailwind.config.js` file in his project. This is Tailwind CSS's configuration file, defining the project's colors, spacing, border radius, and other base styles.

"See, this is your design system." The old-timer pointed at the file. "Tailwind's configuration itself is a design system. You just need to organize this information and tell AI 'only use these colors, these spacing values,' and it won't go off-script."

Xiaoming suddenly understood. He had thought design systems were something sophisticated and advanced, but it turned out to be simply translating Tailwind config contents into plain language and telling AI "you can only use these."

---

## Step 1: Let AI Scan and Generate Design Guidelines

Xiaoming asked: "Do I need to go through Tailwind config manually and organize it into documentation?"

The old-timer laughed: "Of course not. You have AI—let it scan for you."

The old-timer told Xiaoming he could directly ask AI to scan key files in parallel and automatically generate design guideline documentation. Specifically, have AI read simultaneously:

- `tailwind.config.js` - extract design tokens for colors, spacing, border radius, shadows
- `src/components/ui/` directory - list installed shadcn/ui components
- Existing page code - analyze actual style patterns used

Then have AI organize this information into a `DESIGN_SYSTEM.md` document.

Xiaoming tried it, telling AI: "Please scan tailwind.config.js and the src/components/ui directory in parallel, analyze the project's design system, and generate a DESIGN_SYSTEM.md document. The document should include: color system, spacing system, border radius, shadows, typography guidelines, and available component list."

AI quickly generated a complete design guideline document. It clearly listed:

**Color System**: All color definitions extracted from Tailwind config, including primary, secondary, state, and text colors.

**Spacing System**: Spacing values actually used in the project, such as standard values for component padding, component gaps, and page margins.

**Border Radius and Shadows**: Border radius sizes and shadow depths used for different component types.

**Available Components**: After scanning the `src/components/ui/` directory, listed all installed shadcn/ui components including Button, Card, Form, Input, Select, Dialog, Table, etc.

The old-timer said: "See, this is AI's value. It can quickly scan the entire project, extract design patterns, much faster than manual organization. And it won't miss anything."

---

## Step 2: Create a Project Skill

With the `DESIGN_SYSTEM.md` file ready, how do you make AI automatically follow it?

The old-timer said: "The best approach is to create a project-specific Skill."

Xiaoming was a bit confused: "Aren't Skills community-provided best practices? Can I create my own Skill?"

The old-timer smiled: "Of course. Skills can be both general (like frontend-design) and project-specific. You can create a Skill for this project that encapsulates your design guidelines."

**Why create a Skill instead of writing directly in CLAUDE.md?**

The old-timer explained that Skills have an important feature called "progressive loading":

- **Skill description** (name + description): Always in context, telling AI "this project has design guidelines"
- **Skill detailed content**: Only loaded when AI needs it, not occupying context all the time

If you write the entire DESIGN_SYSTEM.md content into CLAUDE.md, it would consume massive context every conversation, even when you're just modifying a backend API.

Xiaoming had AI help him create a project Skill:

```bash
# AI will help you create this structure
.claude/skills/my-project-design/
├── SKILL.md              # Core description
└── references/
    └── DESIGN_SYSTEM.md  # Scan-generated design guidelines
```

**SKILL.md content is simple:**

```yaml
---
name: my-project-design
description: Design system and component library constraints for this project. Use when creating or modifying UI components. Includes Tailwind config, shadcn/ui component inventory, custom component notes, B-end form/table specifications.
---

# Project Design System

This project uses shadcn/ui + Tailwind CSS.

## When to Read Detailed Guidelines

When creating or modifying UI components, please read [DESIGN_SYSTEM.md](references/DESIGN_SYSTEM.md) to understand:
- Available colors, spacing, border radius, and other design tokens
- Installed shadcn/ui component inventory
- Custom business components (like DataCard)
- Special constraints for B-end scenarios (form grouping, table operation confirmations, etc.)
```

The old-timer said: "See, SKILL.md is just a few lines, telling AI 'this project has design guidelines, read references/DESIGN_SYSTEM.md when needed.' This way AI won't be burdened by design guidelines when writing backend code, but will automatically load detailed guidelines when writing frontend code."

---

## Step 3: Let Skills Auto-Trigger

After creating the Skill, Xiaoming discovered a magical phenomenon: he didn't need to say "please follow design guidelines" every time—AI would know on its own.

When he said "create a user list page," AI would automatically:
1. See `my-project-design` Skill's description: "Use when creating or modifying UI components"
2. Determine that "create user list page" falls under "creating UI components"
3. Automatically load this Skill
4. Read references/DESIGN_SYSTEM.md
5. Generate code according to the guidelines

The old-timer said: "This is the value of Skills. You don't need to remember to remind AI every time; AI will automatically load the appropriate Skill based on task type."

---

## Regular Updates: Keeping Guidelines Current

As the project grew, Xiaoming kept adding new components and adjusting design guidelines. How to keep DESIGN_SYSTEM.md updated?

The old-timer said: "Just have AI rescan periodically."

For example, after adding new components, have AI rescan the `src/components/` directory and update `references/DESIGN_SYSTEM.md`. Or after modifying Tailwind config, have AI re-extract design tokens.

This process is fast, completed in seconds. AI will compare old and new versions, only updating changed parts while preserving manually added notes and constraints.

Xiaoming found this "let AI scan and generate" approach much easier than maintaining documentation manually. He didn't need to remember every detail—just periodically have AI refresh, and the Skill's documentation would stay current.

---

## Community Power: General Skills

After creating his project-specific Skill, Xiaoming thought of another question: "This Skill only works for this project. Are there general frontend development best practices I can use across all projects?"

The old-timer said: "Yes. The community has already organized common tech stack best practices into general Skills. You can install and use them directly."

**Project Skill vs General Skill:**

- **Project Skill** (like `my-project-design`): Project-specific design guidelines, custom components, business constraints
- **General Skill** (like `frontend-design`): shadcn/ui usage guide, Tailwind best practices, responsive design patterns

Both can be used together. When you create UI components, AI will load simultaneously:
1. `frontend-design` - shadcn/ui usage guide, Tailwind best practices, responsive design
2. `my-project-design` - this project's specific design guidelines

For example, Anthropic's official `frontend-design` Skill includes shadcn/ui component usage guides, Tailwind CSS best practices, responsive design patterns, accessibility support, and more.

Installation is simple, just one command:

```bash
npx skills add https://github.com/anthropics/skills --skill frontend-design
```

After installation, AI will automatically load this Skill when needed. You don't need to manually remind "use shadcn/ui" or "follow best practices" every time—AI will know on its own.

The old-timer said: "General Skills solve the 'how to do it' problem, project Skills solve the 'what it should look like' problem. Combined, AI knows both best practices and your project guidelines."

---

## Special Needs for B-End Scenarios

Xiaoming's project is a backend management system, a typical B-end (business-facing) application. He found B-end and C-end (consumer-facing) design needs are quite different.

C-end pursues "good looks," B-end pursues "good usability." C-end can have fancy animations; B-end needs "get tasks done quickly." C-end can simplify features; B-end often needs "fit dozens of fields on one page."

The old-timer said: "B-end design guidelines differ from C-end. You need to add B-end-specific constraints to your guidelines."

For example:

**Form Guidelines**: B-end forms often have dozens of fields. Require field grouping (wrapped in Cards), each group with clear headings. Required fields marked with red asterisks. All fields need Zod validation. Submit buttons must have loading states.

**Table Guidelines**: B-end tables need sorting, pagination, filtering. Action column buttons need secondary confirmation (especially for delete operations). Tables need empty state prompts.

**Permission Control**: B-end applications usually have complex permission systems. Different roles see different pages. Design guidelines should state "all pages must consider permission control, hide features users don't have access to."

After writing these B-end-specific guidelines into DESIGN_SYSTEM.md, AI-generated code will automatically conform to B-end usage patterns.

---

## From "Supervisor" to "Director"

Following the old-timer's advice, Xiaoming completed three steps:

1. Had AI scan the project and generate DESIGN_SYSTEM.md
2. Created project Skill (`my-project-design`), putting DESIGN_SYSTEM.md in the references/ directory
3. Installed general Skill (`frontend-design`)

The whole process took just over ten minutes.

Since then, he found AI-generated code consistency improved significantly. No more watching over it "this color is wrong" or "that border radius is wrong"—AI would follow the rules on its own. He went from "supervisor" to "director"—just saying "create a user list page," and AI would automatically:

- Use `frontend-design` best practices (responsive design, accessibility support)
- Follow `my-project-design` project guidelines (correct colors, spacing, components)
- Generate high-quality code matching the project style

More importantly, this process is **repeatable**. Every time the project changes, he just has AI rescan and update references/DESIGN_SYSTEM.md in the Skill, and guidelines automatically update. He doesn't need to manually maintain complex design documentation—AI does it for him.

The old-timer said: "This is the design system of the AI era. What used to take design teams months to build, now takes AI seconds to scan and generate. You define the rules, AI executes the rules. You don't need to know every CSS detail, but you need to know 'what should my product look like.' This is your product—you're in charge."

---

## Team Collaboration: Create Once, Sync Everywhere

Xiaoming asked: "If it's team development, does everyone need to create the Skill?"

The old-timer said: "No. Commit the `.claude/skills/my-project-design/` directory to your Git repository, and others can use it after pulling the code."

This is the benefit of Skills. Previously, when collaborating, design guidelines might only exist in designers' heads or be scattered across various documents. Developers didn't know what colors or spacing to use, so they just went by feel.

Now with project Skills, everyone's AI automatically loads the same guidelines, naturally producing consistent code. New team members don't need to spend time learning design guidelines—AI automatically helps them follow them.

The old-timer said: "This is the power of Skills. Guidelines are no longer passed down verbally but encapsulated in Skills that AI can automatically trigger and load. The larger the team, the more valuable this becomes."

---

## Summary

Xiaoming learned:

- **No need to manually organize design guidelines**—have AI scan the codebase in parallel to auto-generate DESIGN_SYSTEM.md
- **Creating project Skills is the preferred approach**—put DESIGN_SYSTEM.md as a references file for progressive loading
- **Skills auto-trigger**—AI automatically loads appropriate Skills based on task type, no need to remind every time
- **General Skill + Project Skill**—former provides best practices, latter provides project guidelines
- **Rescan regularly**—keep design guideline docs in Skills synchronized with the codebase
- **Team collaboration made easier**—commit Skills to Git for automatic team synchronization

The old-timer said: "VibeCoding's core is 'you set the rules, AI executes.' Skills are the best way to set rules. You don't need to manually organize design guidelines—let AI scan the codebase, and it will automatically extract design patterns. Then encapsulate these guidelines as Skills, and AI will automatically load and follow them when needed."

Xiaoming nodded. He finally understood why large companies spend so much time building design systems—not for looks, but for **consistency**. And in the AI era, the cost of building design systems has dropped dramatically. What used to take design teams months now takes AI seconds to scan and complete. But the value of design systems has actually increased, because they constrain not just people, but AI.

---

::: tip Practical Advice
- Have AI scan tailwind.config.js and src/components/ directory in parallel to auto-generate DESIGN_SYSTEM.md
- Create project Skill (like `my-project-design`), put DESIGN_SYSTEM.md in references/ directory
- Write clear trigger conditions in SKILL.md ("Use when creating or modifying UI components")
- Install general Skills (like `frontend-design`) to improve AI's frontend code quality
- After adding new components or modifying config, have AI rescan to update Skill documentation
- Include `.claude/skills/` directory in Git management for automatic team synchronization
:::

::: info Next Step
You've mastered the core knowledge of UI/UX. Next, Chapter 6 will cover data persistence—how to make your application "remember" user data.
:::