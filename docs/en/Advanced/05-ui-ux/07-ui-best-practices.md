---
title: "5.7 UI Best Practices"
description: "How to constrain AI with design rules, add or optimize components, and verify designs through code"
---

# 5.7 UI Best Practices

> **Goal of this section**: Learn how to define UI design visual interaction rules and constrain AI to follow them, add and optimize components in a component library, and understand the design-code interactive verification workflow.

---

## Xiaoming's New Discovery

Xiaoming managed to give AI "design memory" with Skills (Section 5.6), but he quickly ran into new problems.

As the project grew, shadcn/ui's built-in components weren't enough—for example, the project needed a "stats card" component that displayed numbers, trend arrows, and percentage changes. shadcn/ui didn't have this component, so AI wrote one from scratch every time, and the styling came out differently each time.

What bothered him even more was that he had a set of visual rules in his head: primary colors shouldn't be used randomly, spacing should be consistent, and interaction animations should be uniform. But these rules only existed in his mind—AI had no idea about them.

The old-timer identified the problem: "What you need isn't just AI remembering a component list—you need to establish a **complete set of visual interaction rules** that AI follows like law."

---

## Step 1: Define Visual Interaction Rules

### What Are Visual Interaction Rules

Visual interaction rules go a step further than a design system (the colors, spacing, and border radius covered in Section 5.6)—they define not just "what things look like," but also "how they move" and "how they interact."

The old-timer gave an example:

> The design system says: Buttons use blue, 8px border radius.
> Visual interaction rules say: Buttons darken by 10% on hover, scale to 0.98 on click, show a spinning icon and disable clicks while loading, and display a green checkmark for 1.5 seconds upon completion before reverting.

See the difference? One defines static "appearance," the other defines dynamic "behavior."

### How to Define Them

The old-timer suggested organizing rules into five dimensions:

**1. Color Usage Rules**

```markdown
## Color Usage Rules
- Primary action buttons: Use only primary color (e.g., "Submit," "Save")
- Dangerous action buttons: Use only destructive color (e.g., "Delete," "Reset")
- Secondary action buttons: Use secondary color or outline variant (e.g., "Cancel," "Back")
- Never have two primary buttons in the same row
- State colors must not be mixed: success only for success feedback, warning only for warnings—don't use warning colors as decoration
```

**2. Spacing and Layout Rules**

```markdown
## Spacing and Layout Rules
- Page outer margins: px-6 (mobile: px-4)
- Card inner padding: p-6
- Sibling element spacing: space-y-4 (vertical) or gap-4 (horizontal)
- Form field spacing: space-y-6
- Group title to content spacing: space-y-2
- Page title to content spacing: mb-8
```

**3. Interaction Feedback Rules**

```markdown
## Interaction Feedback Rules
- All clickable elements must have a hover state change
- Buttons must show visual feedback within 200ms of click (color change, scale, or loading animation)
- Async operations must show loading state and disable the button during the operation
- Successful operations use toast notifications, auto-dismiss after 3 seconds
- Failed operations use toast notifications (destructive variant), requiring manual dismissal
- Destructive actions (delete, reset) must require a second confirmation
- After successful form submission, clear the form or navigate away—don't stay on the same page
```

**4. Animation Rules**

```markdown
## Animation Rules
- Page transitions: Use fade transition, 200ms duration
- Modal open/close: Use scale + fade, 150ms duration
- List item add/remove: Use slide + fade, 300ms duration
- Hover effects: Duration no more than 150ms
- No purely decorative animations exceeding 500ms
- All animations must support the prefers-reduced-motion media query
```

**5. Component Usage Rules**

```markdown
## Component Usage Rules
- Forms must use Form + FormField + FormItem combination, never raw <input>
- Data tables must use the DataTable component, supporting sorting and pagination
- Confirmation dialogs use AlertDialog, not Dialog
- Dropdowns with more than 5 options must support search
- Images must have alt attributes and a loading failure fallback
- Icons must use Lucide React only—no other icon libraries
```

Xiaoming asked: "Where do I put these rules after writing them?"

The old-timer said: "In your project Skill. Remember the `DESIGN_SYSTEM.md` you created in Section 5.6? Add these rules there, or create a separate `INTERACTION_RULES.md` in the `references/` directory."

```
.claude/skills/my-project-design/
├── SKILL.md
└── references/
    ├── DESIGN_SYSTEM.md       # Static specs: colors, spacing, border radius
    └── INTERACTION_RULES.md   # Interaction feedback, animations, component usage rules
```

---

## Step 2: Adding New Components to the Library

Xiaoming's project needed a `StatsCard` component, but shadcn/ui didn't have one. The old-timer taught him a three-step approach.

### 2.1 Define First, Then Implement

Don't just say "write me a stats card component"—that gives AI too much creative freedom. Define the component's "spec" clearly first.

Xiaoming told AI:

```
Please create a StatsCard component with the following requirements:

## Component Spec
- File location: src/components/ui/stats-card.tsx
- Built as a wrapper around shadcn/ui's Card component
- Uses project's Tailwind design tokens (refer to DESIGN_SYSTEM.md)

## Props Definition
- title: string - Name of the statistic (e.g., "Total Users")
- value: string | number - The statistic value
- change?: number - Percentage change (positive shows green up arrow, negative shows red down arrow)
- icon?: LucideIcon - Icon on the left side
- loading?: boolean - Loading state, shows skeleton

## Interaction Rules
- Card lifts slightly on hover (translateY -2px) with deeper shadow
- Loading state uses Skeleton component as placeholder
- Zero change percentage shows a gray dash icon

## Accessibility
- Use semantic HTML tags
- Value changes should have aria-label describing the trend
```

### 2.2 Follow the Component Library's Code Style

The old-timer emphasized that new components must match the existing shadcn/ui code style. He had Xiaoming add to the prompt:

```
Please follow shadcn/ui's component coding style:
- Wrap with React.forwardRef
- Use cva (class-variance-authority) for defining variants
- Use cn() utility function for merging className
- Export component type definitions
- Follow the file structure of src/components/ui/card.tsx
```

### 2.3 Update the Component Inventory in the Skill

After creating the component, don't forget to update the available components list in `DESIGN_SYSTEM.md`. Have AI rescan the `src/components/ui/` directory and add the new component.

```
Please rescan the src/components/ui/ directory, update the component list in
DESIGN_SYSTEM.md, and add the new StatsCard component including its Props
documentation and use cases.
```

This way, the next time AI needs to display statistics, it will know to use `StatsCard` instead of writing something from scratch.

---

## Step 3: Optimizing Existing Components

As the project progressed, Xiaoming found the existing Button component was missing some states. He needed to optimize it but was afraid of breaking things.

### The Optimization Workflow

The old-timer taught a safe optimization workflow:

**1. Describe the Problem, Don't Jump to Changing Code**

```
Our Button component (src/components/ui/button.tsx) needs optimization:

Current issues:
- No loading variant—can't show loading state during form submission
- No icon-only variant—toolbar icon buttons have inconsistent styling

Expected results:
- Add loading prop that shows a spinning icon and disables clicks when true
- Add size="icon" variant that provides a square icon-only button style

Constraints:
- Must not break existing button usage
- New variants must be compatible with existing ones (e.g., loading + variant="destructive" should work)
- Must follow the interaction feedback rules in INTERACTION_RULES.md
```

**2. Have AI Output a Change Plan Before Making Changes**

```
Please analyze the current Button component code structure first and output your planned changes:
- Which code needs to be modified
- What new code will be added
- What potential compatibility risks exist

Don't modify code yet—let me confirm the plan first.
```

**3. Verify the Optimization Results**

After optimization, have AI check compatibility:

```
Please check all places in the project that use the Button component and confirm:
1. All existing usages still work correctly
2. The new loading and icon size variants have usage examples
3. TypeScript types are correct
```

---

## Design-Code Interactive Verification

Xiaoming asked a pointed question: "We used to draw designs in Figma first, then have developers code to match. Now we use AI to write code directly—is Figma still useful?"

The old-timer thought for a moment: "Figma is certainly still useful, but the workflow has changed."

### Traditional Workflow vs. AI-Era Workflow

**Traditional Workflow (Waterfall):**
```
Designer creates Figma mockup → Review → Developer codes to match → QA checks fidelity → Iterative revisions
```
Problem: Design and code are separate, with significant translation costs and information loss in between.

**AI-Era Workflow (Interactive):**
```
Describe requirements in natural language → AI generates code → See results in browser → Describe adjustments → AI modifies → Repeat
```
Design and code merge into one—what you see is what you get.

### Code Is the Design

The old-timer said: "In the AI era, code itself is the best design artifact. It's interactive, it's real, and there's no 'fidelity' problem—because what you see is exactly what will ship."

This doesn't mean design thinking is less important. Quite the opposite—**design thinking becomes even more important**—because you've saved the time spent translating from Figma to code, so you can spend more energy thinking about "is this design right?"

### The Practical Verification Workflow

Following the old-timer's advice, Xiaoming established this workflow:

**1. Describe → Generate → Verify**

```
Describe the interface requirements in natural language:
"Create a user profile page. At the top, show the avatar and username.
Below that, a personal info form with name, email, and phone number.
Save button in the bottom-right corner of the form. Single-column layout on mobile."

→ AI generates code
→ View results in the browser
→ Spot issues: "Avatar is too small, save button isn't prominent enough"
→ Continue adjusting with natural language
```

**2. Screenshot Comparison Verification**

When you have a reference design (whether it's a Figma mockup, Dribbble screenshot, or competitor screenshot), you can show it directly to AI:

```
Please reference this screenshot's layout and style to adjust the current page. Note:
- Maintain our project's design specs (colors, spacing, border radius per DESIGN_SYSTEM.md)
- Only reference the layout structure and information hierarchy—don't copy the colors
```

**3. Cross-Device Verification**

```
Please check this page's appearance at the following sizes:
- Mobile (375px)
- Tablet (768px)
- Desktop (1280px)

If there are layout issues, please fix them directly.
```

### The Future of Design Tools

Xiaoming asked: "So are design tools like Figma going to be replaced?"

The old-timer smiled: "The tools won't disappear, but their role will change."

- **Design tools' new role**: Shifting from "creating final mockups" to "rapidly exploring ideas." When you need to compare 5 layout options simultaneously, Figma's drag-and-drop is more efficient than writing prompts.
- **AI design tool convergence**: Tools like v0.dev are already bridging the gap between design and code—you describe your requirements, and it gives you runnable code.
- **The core shift**: It used to be "design first, code follows." Now it's "design and code happen simultaneously." Design verification is no longer about comparing screenshots—it's about interacting with the real interface directly in the browser.

The old-timer summarized: "**The essence of design is solving user problems, not drawing pretty pictures**. Tools are just means to an end. AI lets you skip the 'draw mockup → write code' translation step, but the 'understand users → define solutions' thinking step will never be skipped."

---

## A Complete System for Design Consistency

Tying everything together, the old-timer helped Xiaoming summarize a complete approach:

### 1. Set the Rules

Define visual interaction rules, write them into `INTERACTION_RULES.md`, covering five dimensions: colors, spacing, interaction feedback, animations, and component usage.

### 2. Store the Memory

Store the rules in your project Skill (Section 5.6's `my-project-design`), so AI automatically loads them when needed.

### 3. Extend Components

For components the project needs, define specs first then have AI implement them, following the component library's code style. Update the component inventory in the Skill after completion.

### 4. Optimize Regularly

When components fall short or have issues, describe the problem → confirm the plan → implement changes → verify compatibility.

### 5. Verify Directly

View results directly in the browser instead of pixel-comparing against mockups. Code is the ultimate expression of design.

---

## Summary

Xiaoming learned:

- **Visual interaction rules** include not just colors and spacing, but also interaction feedback, animations, and component usage standards
- **Adding components** requires defining specs first, following existing code style, then updating the Skill afterward
- **Optimizing components** means describing problems rather than jumping into code, confirming plans before implementing, and verifying compatibility afterward
- **Design and code can be verified interactively**—seeing real results in the browser is more efficient than comparing against mockups
- **Design tools won't disappear, but their role will change**—from creating final mockups to exploring ideas, while core design thinking remains irreplaceable

The old-timer said: "The core of UI best practices is simple—**you set the rules, AI follows the rules, the browser validates the rules**. The clearer the rules, the more consistent AI's output. Don't chase perfect mockups—chase runnable great experiences."

---

::: tip Practical Advice
- Spend 30 minutes defining your project's visual interaction rules and write them into `INTERACTION_RULES.md`
- When adding components, write a "component spec" prompt first, including Props, interaction rules, and accessibility requirements
- When optimizing components, have AI output a change plan first and confirm before making modifications
- Build a habit of verifying directly in the browser to reduce the translation cost between mockups and code
- After every component change, have AI rescan and update the Skill documentation
:::

::: info Looking Back
This section is the final practical guide in the UI/UX chapter. You've now mastered the complete knowledge chain from design tools (5.1), component libraries (5.2), animation libraries (5.3), inspiration sources (5.4), advanced effects (5.5), AI design memory (5.6), to UI best practices (5.7). Up next is Chapter 6—Data Persistence.
:::
