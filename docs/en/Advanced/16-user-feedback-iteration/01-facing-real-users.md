---
title: "16.1 Facing Real Users"
description: "Post-launch mindset adjustment and building feedback channels"
---

# 16.1 Facing Real Users

> Data tells you what happened, but user feedback tells you why it happened.

---

## The First Day After Launch

Xiaoming's note-taking app finally went live. He opened Umami and saw people visiting—real people were actually using something he built.

But the excitement quickly turned into confusion. He could see people arriving, people leaving, and people staying on certain pages for a long time. But he had no idea what they were thinking. Did they find it easy to use? Or could they not find the button? Did they like the feature? Or were they cursing the design?

Data is silent. It only tells you "what happened," not "why it happened."

---

## The Gap Between Reality and Expectations

Before launch, Xiaoming imagined a flood of users pouring in. The reality was: only a handful of visits a day, some people saying "hard to use," some saying "doesn't look good," and a steady stream of Bugs he never saw coming. Features that seemed obvious to him were impossible for users to find; needs he thought were a perfect match turned out to be things users didn't care about at all.

This gap is normal. Every product goes through this stage after launch.

The root of the gap is simple: you're too familiar with your own product. You know where every button is and how every feature works—but users are seeing it for the first time. What you see is "Settings is the third item in the sidebar"; what users see is "I can't find Settings." What you think is "this only takes a moment"; users think is "why is this so much trouble?" The gap between the developer's perspective and the user's perspective is much bigger than you think.

::: tip User feelings are real

Not every user request is necessarily right, but their feelings are always real. If a user says "I can't find it," then they can't find it—no matter how obvious you think the entry point is.

:::

---

## Common Early User Reaction Patterns

Xiaoming started receiving feedback and noticed that user reactions generally fell into a few categories.

The biggest group was the silent majority—people who used the product but said nothing, leaving him with no idea what they thought. For these users, you need to reach out and talk to them proactively. Then there are bug reporters, the people who tell you when they find a Bug. These people are gold—thank them and document everything carefully. There are also feature wishers, users who suggest all kinds of things like, "Could you add XX?" For those requests, you need to prioritize carefully—you can't build every idea that comes in.

The group that made Xiaoming the most uncomfortable was the direct critics—people who pointed out problems without sugarcoating anything. But the old hand told him these users are actually the most valuable, because they're telling the truth. Finally, there's a small group of enthusiastic supporters who genuinely love your product. Treasure them, and dig into why they like it so much—that can help you uncover your product's core value.

---

## Adjusting Your Mindset

When Xiaoming received his first piece of negative feedback, it hit him hard. He had spent months building this thing, and someone dismissed it with a single "not easy to use."

The old hand said, "The criticism isn't personal. Users don't know how many late nights you pulled—they only care whether the product is easy to use. That's actually a good thing—it means they care. What's really scary isn't getting criticized. It's being ignored."

The key to adjusting your mindset can be summed up in four words: lower your expectations. Early products are never perfect—this is the starting point, not the finish line. Treat criticism as free product consulting, and treat every problem as a chance to improve. Most importantly, be patient—success takes time to build, and no product becomes a smash hit on day one.

### From Developer to Operator

After launch, Xiaoming's role quietly changed. He used to focus on code quality; now he has to focus on user experience. He used to chase perfect features; now he has to chase user retention. He used to think like an engineer; now he also needs to think like a product person.

His daily work changed too—in addition to writing code, he now has to look at data, respond to feedback, and think about what to improve next. This transition isn't easy, but it's what takes a product from "usable" to "great to use."

---

## What to Focus on Early On

The old hand reminded Xiaoming: early on, don't obsess over user count—that's a vanity metric.

What really matters is user retention—do users come back? That's the core question. If 100 people show up and not a single one returns the next day, then that 100 means nothing. Beyond retention, you should also watch core feature usage (are users actually using your main feature?), issue feedback (what problems are users running into?), and usage scenarios (in what situations are users using the product?).

On the other hand, total user count, revenue, and competitors—none of those matter much early on. It's normal to have few users at the beginning, talking about revenue before validating demand is meaningless, and obsessing over competitors only distracts you. Focus on your own users and serve them well.

---

## Build a Support System

The easiest mistake for indie developers to make is trying to carry all the pressure alone. When you're facing negative feedback, user churn, and a steady stream of Bugs, it's easy to fall into self-doubt if you have no one to talk to.

Find a founder community and exchange experiences with other indie developers—you'll realize everyone has gone through the same stage, and you're not alone. Build an early user group; they are your best source of feedback and your most loyal supporters. Find a mentor or a friend—someone who can pull you back up when you're feeling discouraged.

---

## Build Feedback Channels

Xiaoming realized that looking at Umami data alone wasn't enough—he needed to hear users' voices. But feedback doesn't just come to you on its own. You need to actively create channels for it.

Different channels collect different kinds of feedback. An in-app feedback button is the most direct: users can submit issues on the spot when they run into them, with full context, but it requires users to take the initiative. Email is better for deeper suggestions and back-and-forth discussion, but responses are slower. Comments on social media spread widely, but they're scattered and unsystematic. User groups can create a sense of community and generate ongoing discussion, but they require time to maintain. User interviews provide the deepest insights, but they also take the most time.

Xiaoming didn't need to build everything at once. He started with two things: adding a feedback button inside the product, and creating a WeChat group.

### In-App Feedback Button

The most direct approach is to add a feedback entry point inside the product. Placement matters—fixed buttons on the page make it easy to submit feedback at any time, a Settings page keeps it from interfering with the main flow, a post-action popup works well for collecting feedback on specific features, and an automatic popup when an error occurs can capture the most valuable error context.

The feedback form should be simple and ask only for necessary information. Let users choose a feedback type (Bug, feature suggestion, other), automatically collect the current page and browser information, and show a confirmation message after submission. Just tell Claude Code that you need a feedback component, and clearly describe the categories and submission logic.

### Social Media

Users will talk about your product on social media whether or not you have an official account. Search for your product name and account mentions on Twitter/X, follow relevant communities on Reddit, search product keywords on Weibo and Xiaohongshu, and check Issues and Discussions on GitHub.

Your response strategy matters too. Thank people for positive reviews and repost them, direct bug reports to your official feedback channels, respond to negative comments by understanding the cause and promising improvement, and document feature requests while explaining their priority. Don't argue with users, even if you think they're wrong.

### User Groups

Creating an early user group is a great way to gather feedback continuously. WeChat groups are the most user-friendly option for users in China and make communication instant; Discord is great for technical products and has clear channel organization; Slack has a more professional atmosphere; Telegram works well for international users and is privacy-friendly.

Xiaoming chose a WeChat group because most of his users were in China. The group doesn't need to be large—20-30 people is enough in the early stage. The key is that you need to be present in the group, actively participating in discussions instead of creating it and then ignoring it.

---

## Data + Feedback = Action

After setting up feedback channels, Xiaoming started comparing Umami data with user feedback and discovered some interesting patterns.

Umami showed a high drop-off rate on the signup page, and at the same time someone in the WeChat group said, "The signup form is too long"—the data told him "where the problem is," and the feedback told him "why the problem exists." Put together, the next step became clear: simplify the signup flow.

Here's another example: usage of a certain feature was very low. At first he thought users simply didn't need it, but someone in the group said, "I can't find this feature"—the issue wasn't lack of demand, it was poor entry-point design.

That's the power of combining data and feedback. If you only look at data, all you know is "the bounce rate is high"; if you only listen to feedback, you can't tell whether it's an isolated case or a widespread problem. Only by comparing the two can you be confident about what to improve.

---

## FAQ

### Q1: What if no one is using it?

Don't rush to scale up just yet. Find a few potential users, demo the product personally, observe how they use it, and understand where the problems are. A few real early users are more valuable than a thousand registrations.

### Q2: What if no one gives feedback?

That's normal—most users won't actively give feedback. Reach out to a few users, ask them to try the product, and ask directly. Don't sit around waiting for feedback to come to you.

### Q3: What if there's too much negative feedback?

First, stay calm. Negative feedback is the most valuable kind—it points to the real problems. Categorize it, and fix the issues with the biggest impact first.

### Q4: When should I give up?

If there has been no user growth or retention improvement for several months in a row, it may be time to reconsider your direction. But at least give yourself enough time—most products do not succeed overnight.

---

Xiaoming now has feedback channels in place and is starting to receive all kinds of feedback. Some people say the colors are ugly, some say login crashes, and some want an export feature. Looking at the pile of feedback, he doesn't know what to tackle first.

In the next section, we'll learn how to prioritize feedback.