---
title: "B. Common Errors and the “Ask AI” Workflow"
order: 2
---

# B. Common Errors and the “Ask AI” Workflow

For the basic version, the most recommended way to troubleshoot is not to start changing things randomly yourself, but to first describe the issue clearly and then let AI help narrow down the scope. This page summarizes the most common types of blockers.

## General Question Framework

No matter what kind of problem you run into, you can start with this framework:

```text
The problem I’m currently encountering is: ______

What’s happening: ______
What I just did: ______
What I expected to happen: ______

If there is an error, here is the full error message:
______

Please first help me determine the most likely cause, then tell me what I should check next—and only that one thing first.
```

## 1. Blank Page / Won’t Open

| What to check first | How to ask AI | Common fix directions |
|------|-----------|--------------|
| Is it local or online? | The page is blank and not rendering properly. Please first determine whether this is a startup failure, a routing issue, or a runtime error. | Dependencies not installed correctly, wrong startup command, runtime error, build failure after deployment |

When providing additional information, prioritize:

- Screenshot of the page
- Any message shown in the browser
- The full terminal error output

## 2. Button Doesn’t Respond / Link Won’t Navigate

| What to check first | How to ask AI | Common fix directions |
|------|-----------|--------------|
| Is it a visual button or a real interaction? | This button looks clickable, but nothing happens when I click it. Please check whether it currently only has styling and is not actually bound to a real link or event. | Placeholder button only, no event binding, link URL not connected, interaction logic not completed |

These issues are especially common in Chapters 1 and 4, particularly when the platform first generates a page that “looks like a finished product.”

## 3. Chat Area Doesn’t Reply / Replies Are Abnormal

| What to check first | How to ask AI | Common fix directions |
|------|-----------|--------------|
| Is it a placeholder demo or a real call? | Please first confirm whether the current chat area is a keyword-based demo, a fake-data reply, or actually connected to a model API. | API not connected yet, environment variables not configured, API request failed, unclear boundaries in the specification |

If the chat area can already reply, but the content is wrong, you can add this sentence:

```text
The chat can reply now, but the answers are often off-topic / very mechanical.
Please do not change the frontend layout yet. First check whether the digital avatar specification and context are missing information.
```

## 4. Local Run Failure / Build Failure

| What to check first | How to ask AI | Common fix directions |
|------|-----------|--------------|
| Full logs | Here is the full error output when I run / build. Please first determine which layer is most likely causing the issue, then tell me what I should change next—and only that one thing. | Missing dependencies, wrong command, incompatible Node version, incorrect config file, missing environment variables |

The most important thing with this type of issue is: **don’t just say “there’s an error.”** Paste the full log so AI can help you locate the problem much faster.

## 5. Deployment Failure / Online Behavior Differs from Local

| What to check first | How to ask AI | Common fix directions |
|------|-----------|--------------|
| Deployment logs, environment variables, online behavior | Here are the logs from the deployment platform. Please first determine whether the issue is with the build command, environment variables, or platform configuration. | Wrong build command, missing environment variables, incorrect online asset paths, mismatch with platform default configuration |

These issues are most common in Chapter 6. Before deployment succeeds, check the logs first; after deployment succeeds, then check whether the live link still has styling or chat issues.

## A Very Important Troubleshooting Principle

Change only one variable at a time.

Don’t immediately change the model, prompt, layout, API, and environment variables all at once as soon as you see a problem. A more reliable approach is to first judge which layer the problem most likely belongs to, and then start investigating from that layer. That makes it much easier to know which step actually made the difference.