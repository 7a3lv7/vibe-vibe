---
title: "16.2 Feedback Categorization and Prioritization"
description: "Use the RICE framework to prioritize feedback"
---

# 16.2 Feedback Categorization and Prioritization

> After collecting a pile of feedback, how do you decide what to tackle first? Two key dimensions are scope of impact and severity.

---

## Out of 20 Pieces of Feedback, Which Should You Do First?

Xiaoming set up his feedback channels and received more than 20 pieces of feedback within a week:

- "It occasionally crashes during login"
- "Can you add dark mode?"
- "When will the export feature be available?"
- "The homepage colors are too ugly"
- "The buttons are too small on mobile, I can't tap them"
- "It's loading way too slowly"
- ……

Each one seems reasonable, but he can't do 20 things at once. The old hand said: "Categorize first, then prioritize."

---

## Step 1: Categorize

Feedback comes in all shapes and sizes, but it usually falls into just a few categories. Bug reports are functional errors or abnormal behavior, such as "the login button doesn't respond." Feature requests are requests for new functionality, such as "could you add an export feature?" UX issues are problems that make the product awkward to use, such as "I can't find where the settings are." Performance issues are about speed or lag, such as "the page loads too slowly." There is also content-related feedback, such as "this word is used incorrectly." Everything else goes into other.

The purpose of categorization is not to make things look neat, but to prepare for the next step—determining priority. Bugs and performance issues are usually more urgent than feature requests because they affect whether the product "works at all," not whether it "works well."

---

## Step 2: Determine Urgency

The old hand taught Xiaoming to judge priority using two dimensions: scope of impact (how many people are affected) and severity (how serious the problem is).

P0 is the most urgent—affecting a large number of users and making the system unusable, such as login being broken, and it needs immediate attention. P1 is high priority—affecting a large number of users and impacting core functionality, such as payment failures, and should be resolved within 24 hours. P2 is medium priority—affecting some users but with a workaround available, such as display issues in a certain browser, and should be handled within the week. P3 is low priority—low impact and does not block usage, such as unattractive colors, and can wait until there is time.

When you cross these two dimensions, you get a matrix:

```
Severity
High │ [P0] Urgent Fix      [P1] High Priority
     │
Med  │ [P2] Medium Priority
     │
Low  │                    [P3] Low Priority
     └──────────────────────────────
       Large          Small
          Scope of Impact
```

Xiaoming placed the 20 pieces of feedback into the matrix, and things immediately became clear. "Login crashes" has broad impact and high severity, so it is P0; "the colors are too ugly" has limited impact and low severity, so it is P3. This step alone split the 20 pieces of feedback into four levels, and he knew what to do first.

<PriorityMatrix />

---

## RICE: More Precise Prioritization

The matrix is great for quickly sorting feedback into broad buckets, but what if you have several P1 items? The old hand taught Xiaoming a more precise method—the RICE scoring model.

RICE consists of four dimensions. **Reach** is how many users are affected, measured by monthly users. **Impact** is how much it affects users, scored from 1 to 3. **Confidence** is how confident you are in the estimate, expressed as a percentage. **Effort** is how much time and resources are required, measured in person-months.

The formula is simple:

```
RICE = (Reach × Impact × Confidence) / Effort
```

Xiaoming tried scoring three features:

| Feature | Reach | Impact | Confidence | Effort | RICE |
|------|--------|--------|-----------|--------|------|
| Export Feature | 100 | 3 | 80% | 2 | 120 |
| Dark Mode | 500 | 1 | 100% | 3 | 167 |
| Fix Login Bug | 1000 | 3 | 100% | 1 | 3000 |

The result is crystal clear: fixing the login bug has a RICE score 18 times higher than dark mode. Intuitively, you might think "500 people want dark mode, so that should come first," but RICE tells you that fixing a severe bug affecting 1000 people is far more important than satisfying a "nice to have" for 500 people.

<RICECalculator />

::: tip The Value of RICE

RICE is not about calculating with perfect precision. It is about putting your assumptions and data on the table. When you're stuck on "what should we do first," laying out the numbers often makes the answer obvious.

:::

If RICE feels too heavyweight, there is also a simplified version called ICE: Impact (1-10), Confidence (1-10), and Ease (1-10), averaged together. ICE is suitable for quick decisions, while RICE is better for situations that require more rigorous evaluation.

---

## Feedback Management Workflow

With categorization and prioritization in place, Xiaoming set up a simple feedback handling process:

<FeedbackManagementFlow />

You can manage this workflow with GitHub Issues or Notion. The key is not the tool, but building the habit of "collect → categorize → prioritize → resolve → respond." The last step in particular—"notify the user"—is especially important. When you fix an issue a user reported, tell them. It makes users feel heard, and they will be more willing to give feedback again in the future.

---

## Learn to Say "No"

The hardest lesson for Xiaoming to learn was how to say no.

Someone says, "Can you add chat?" Someone else says, "Can you support Markdown?" Another says, "Can you build a mobile app?" Every suggestion sounds good, but your time and energy are limited.

Core features take priority over peripheral ones, fixing bugs takes priority over new features, small investments with big returns take priority over large investments with small returns, and requests aligned with the product direction take priority over those that are not.

When you need to decline something, do not say "we won't do it" coldly. If it is not a need from your target users, you can say, "This is not in our current plan, but thank you for the suggestion." If it does not fit the product direction, you can say, "Our focus is X, so we won't be doing Y for now." If it is simply a matter of limited resources, you can say, "This is a great suggestion. We've recorded it and will consider it later."

Saying "no" is not about ignoring users; it is about being responsible for the product direction. You are the product owner, and not every piece of feedback needs to be adopted. The old hand said: "A great product is not the one with the most features, but the one with the sharpest focus."

---

## Frequently Asked Questions

### Q1: What if all feedback seems important?

Use the RICE or ICE framework to score it objectively. Once you put the numbers down, the priorities usually become clear. The feeling that "everything is important" often comes from not having a quantified comparison.

### Q2: What if users keep asking when something will be implemented?

Answer honestly. Do not make promises, but you can say, "We're evaluating it and will consider it in a future release." Making a promise and then failing to deliver does more damage to trust than not making a promise at all.

### Q3: How do you avoid being led around by users?

Stay grounded in the product vision. User feedback is important, but you are the product owner. Collect feedback, analyze data, determine priority, and then decide whether to do it. As Henry Ford said: "If I had asked people what they wanted, they would have said a faster horse."

---

Xiaoming learned how to prioritize feedback, but some feedback is still too vague—what exactly does "hard to use" mean? In what way is the "experience bad"?

In the next section, we will learn how to truly understand users.