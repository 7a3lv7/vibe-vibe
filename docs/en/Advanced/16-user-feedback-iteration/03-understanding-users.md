---
title: "16.3 Understanding Users"
description: "Combining user interviews with data-driven insights"
---

# 16.3 Understanding Users

> Data tells you "what happened"; user interviews tell you "why it happened." You need both to make good decisions.

---

## What exactly makes something "hard to use"?

Xiaoming received a piece of feedback: "Your product is hard to use."

He stared at those words and had no idea what to change. Was the interface unattractive? Were the interactions too complicated? Was functionality lacking? Was it loading too slowly?

The seasoned mentor said, "Go ask users. Not with a survey—actually talk to them."

---

## The Mom Test: how to ask questions that get honest answers

Xiaoming scheduled an online chat with a user. He asked, "What do you think of my product?"

The user said, "It's okay, I guess."

After 15 minutes, Xiaoming still hadn't learned anything useful. The seasoned mentor smiled and said, "You asked the wrong questions."

The mentor introduced him to **The Mom Test**—a user interview method built around one core idea: ask the right questions, and even your mom won't be able to lie to you.

This method has three core principles. First, talk about the past, not the future—past behavior is real, and "How did you do it last time?" is far more reliable than "Would you use this in the future?" Second, talk about specifics, not generalities—"What difficulties did you run into last time?" is much more valuable than "What do you think?" Details reveal real problems; vague answers reveal nothing. Third, ask less and listen more—let users do most of the talking while you listen for real needs hidden in the details.

### Good questions vs. bad questions

<InterviewQuestions />

This is the heart of The Mom Test.

"Would you use this feature?" is a bad question, because stated future intent is unreliable—most people will politely say "yes." A better alternative is "How did you solve a similar problem last time?" because that asks about real past behavior.

"Is this feature important?" is also a bad question, because you'll probably get a polite "yes." A better alternative is "When would you need this?" If the user can't describe a specific scenario, they probably don't need it that much.

"What do you think of this design?" is a bad question, because the answer may just be politeness. A better alternative is "What about this design feels confusing?" That gets straight to the points of friction.

"What features do you want?" is a bad question, because users are not product managers—they don't know which features are actually feasible. A better alternative is "What problems have you run into lately?" Start with the problem, then come up with the solution yourself.

---

## Xiaoming's second interview

After learning The Mom Test, Xiaoming scheduled another interview. This time, he changed how he asked questions.

"When was the last time you used my note-taking app?"

"Last week, I think. I wanted to write down some meeting notes."

"Did it go smoothly?"

"Well... actually, I spent a while looking for the button to create a new note. Later I realized I had to choose a category first, but at the time I just wanted to jot something down and didn't want to pick a category."

Xiaoming immediately understood. The "choose a category before creating a note" flow had seemed perfectly reasonable to him—but the user just wanted to capture something quickly. Categorization could come later.

He then asked the user to walk through the process again. While observing, he noticed the user hesitating in several places and even clicking the wrong button once. These were problems the user hadn't even consciously noticed, but Xiaoming could see them clearly.

That's the value of observation. Users may say "it's fine," but hesitation, pauses, and frowns during actual use are the real feedback. Hesitation and pauses mean the user isn't sure what to do, repeated attempts mean the feature isn't intuitive, changes in expression (confusion, frustration, surprise) are the most honest evaluation, and skipping a step may mean they never noticed that feature at all.

---

## Practical interview tips

Xiaoming's second interview went much better, and he wanted to turn the method into a repeatable process. The seasoned mentor helped him organize it into a practical framework.

### How to ask

The core of interviewing is to open up the conversation with open-ended questions, then use follow-up questions to dig into the details.

Open-ended questions generally fall into a few categories: background questions ("Can you tell me about your usage scenario?"), behavior questions ("How did you complete it last time?"), difficulty questions ("What felt confusing?"), and feeling questions ("How did you feel at that moment?"). These questions don't have "right answers," so users can speak freely—and that's how you hear what they really think.

After a user answers, don't rush to the next question. Use follow-ups to turn vague answers into concrete ones: "Can you be more specific?" "Can you give an example?" "How did that compare with the previous solution?" There's also one especially powerful follow-up technique—silence. Say nothing, and let the user keep talking. When a user has only said half of what they mean, don't jump in with a response or advice. Silence encourages them to keep going, and what comes next is often the most valuable part.

### How to run a complete interview

Before the interview, prepare: be clear about which hypothesis you want to validate, and have your core questions ready—don't improvise. At the start, explain the purpose and emphasize that there are no "right answers" so the user can relax. During the interview, let the user demonstrate their workflow, don't interrupt, and use good questions to dig deeper. When taking notes, write down exact quotes instead of just your own summary—your summary may already filter out key information. Organize your notes within 24 hours after the interview, because memory fades.

How many interviews are enough? You don't need that many. Xiaoming did 6 interviews, and by the 4th he was already hearing the same issues repeated. In general, 1–3 interviews are enough to start spotting patterns, 5–10 will surface most recurring issues, and after 10 the rate of new discoveries drops off. If two interviews in a row reveal nothing new, you can stop.

### Turning feedback into action

Once the interviews are done, you have a pile of notes in front of you. How do you go from "what users said" to "what should I change"? The seasoned mentor taught Xiaoming to distill feedback through four layers.

The first layer is raw feedback, such as "I can't find the settings." The second layer is the problem: the settings entry point isn't obvious. The third layer is the insight: users expect to find settings in the top-right corner (because that's where most apps put them). The fourth layer is the action: move the settings button to the top-right corner.

Jumping straight from raw feedback to action is risky—"I can't find the settings" doesn't necessarily mean you should move the button; maybe what you need is onboarding guidance. The middle two layers—"problem" and "insight"—help you understand the real reason so you can find the right solution.

### Common mistakes

The seasoned mentor warned Xiaoming about a few traps beginners often fall into. The most common is pitching the product—you can't resist explaining, "Actually, that feature is here," but once you do that, users stop telling you the truth. Stay neutral. Leading the answer is also common—"Do you think this button is too small?" Users will just agree with you, so let them express themselves freely. Listening only to positive feedback is human nature, but negative feedback is where the gold is. Solving too early is a common engineer habit—the user isn't even finished talking, and you're already thinking about how to change the code. Understand first, solve second.

---

## Qualitative + quantitative: walk on two legs

Interviews gave Xiaoming a lot of answers to the "why." But the seasoned mentor reminded him: "Interviews are small samples. Don't draw conclusions based on just a few people. Compare what you learn in interviews with your Umami data."

Data (quantitative) tells you the What—what happened. It's objective, measurable, scalable, and good for spotting trends and patterns. Feedback (qualitative) tells you the Why—why it happened. It's subjective but deep, small in sample size but rich in context, and good for uncovering background and causes.

When Xiaoming compared his interview findings with Umami data, he found two clear directions for improvement. The data showed a 70% bounce rate on a certain page, and users said in interviews, "I can't find the main entry point to the core feature"—so improve navigation. The data showed registration conversion was only 30%, and users said in interviews, "The form is too long"—so simplify sign-up.

Looking only at data, you know only that "the bounce rate is high." Listening only to feedback, you don't know whether it's an isolated issue or a widespread one. You need both to be confident about what to change.

---

## North Star metric

The seasoned mentor asked Xiaoming, "What's the single most important metric for your product?"

Xiaoming thought for a moment. "User count?"

"No. User count is a vanity metric—it only goes up and doesn't tell you whether the product is actually good. What you need is a **North Star metric**—the number that best reflects your product's core value."

Different types of products have different North Star metrics. For a SaaS tool, it might be monthly active users. For e-commerce, it might be order conversion rate. For a content platform, it might be time spent consuming content. For a utility product, it might be core feature usage rate.

For Xiaoming's note-taking app, the North Star metric might be "number of notes created per week"—that directly reflects whether users are continuing to use the core feature. If that number is going up, the product is getting better. If it's going down, then no matter how much user count grows, something is wrong.

---

## The hypothesis validation loop

With data and feedback in hand, Xiaoming began learning a more systematic way of thinking: hypothesis-driven development.

Not "I think we should change X," but "I hypothesize that changing X will produce Y because of Z." Then you design an experiment to validate that hypothesis.

<HypothesisValidationCycle />

The standard format for stating a hypothesis is: "If [change X], then [expected result Y], because [reason Z]." For example: "If we simplify the registration form, then registration conversion will increase by 20%, because users are more willing to complete a shorter flow."

The most direct way to validate a hypothesis is A/B testing—randomly split users into two groups, show one the old version and the other the new version, change only one factor, and compare the results. The key is a single variable—if you change three things at once, you won't know which one made the difference.

### Case study: Super Daily's registration optimization

Here's a real example. The data showed the registration page had a drop-off rate as high as 70%.

Hypothesis: the registration form is too long and asks for too much information, causing users to abandon it midway.

Experiment: simplify the registration form and keep only the email field, collecting the rest of the information after sign-up.

| Version | Conversion Rate |
|------|--------|
| Original version (7 fields) | 30% |
| New version (1 field) | 55% |

Conversion increased by 83%. A simple change delivered results far beyond expectations. That's the power of a hypothesis-driven approach—you don't make changes based on gut feeling; you make evidence-based changes and validate them with data afterward.

---

## The pitfalls of being data-driven

Data is useful, but it can also mislead you.

The most common trap is "correlation equals causation"—just because A and B happen at the same time doesn't mean A caused B. Ice cream sales and drowning incidents are positively correlated, but buying ice cream doesn't cause drowning; both are driven by summer. To verify causality, you need to design experiments.

Selection bias is also common—looking only at data that supports your viewpoint and ignoring data that doesn't. Make sure you're looking at the full picture, not cherry-picked data.

Short-term thinking is another trap—a change may boost conversion in the short term but hurt retention in the long term. Pay attention to long-term trends, and don't be misled by short-term fluctuations.

There's also data worship—relying entirely on data while ignoring intuition and feedback. Data isn't everything; some things won't show up in the numbers. And then there's over-analysis—spending too much time analyzing and never taking action. Good enough is enough. Start doing, then keep iterating.

---

## Frequently asked questions

### Q1: How do I do analysis with a small amount of data?

When data volume is low, qualitative feedback matters even more. Every user's feedback becomes more valuable, and in-depth interviews are more effective than large-scale statistics. Don't do nothing just because "the sample size isn't big enough."

### Q2: What if what users say doesn't match what they do?

Trust behavior, not words. If a user says "it's easy to use" but keeps frowning while using it, there's a problem. That's why observation is more valuable than asking alone.

### Q3: What if the data and feedback contradict each other?

Trust the data first, but use interviews to understand why. The data may reveal patterns you hadn't noticed.

### Q4: What if I don't have a data team?

You don't need one. Ready-made tools like Umami are enough. Start with simple metrics—retention, core feature usage, and conversion rate. You don't need complex data infrastructure to start being data-driven.

---

Now Xiaoming knows what to change. But how should he change it? All at once? Or in stages? And once it's changed, how should he communicate that to users?

In the next section, we'll talk about the rhythm of iteration and growth.